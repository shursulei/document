Error:"org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper" when connection to Beeline





```
 static void configureConnParams(JdbcConnectionParams connParams)
      throws ZooKeeperHiveClientException {
    String zooKeeperEnsemble = connParams.getZooKeeperEnsemble();
    String zooKeeperNamespace =
        connParams.getSessionVars().get(JdbcConnectionParams.ZOOKEEPER_NAMESPACE);
    if ((zooKeeperNamespace == null) || (zooKeeperNamespace.isEmpty())) {
      zooKeeperNamespace = JdbcConnectionParams.ZOOKEEPER_DEFAULT_NAMESPACE;
    }
    List<String> serverHosts;
    Random randomizer = new Random();
    String serverNode;
    CuratorFramework zooKeeperClient =
        CuratorFrameworkFactory.builder().connectString(zooKeeperEnsemble)
            .retryPolicy(new ExponentialBackoffRetry(1000, 3)).build();
    try {
      zooKeeperClient.start();
      serverHosts = zooKeeperClient.getChildren().forPath("/" + zooKeeperNamespace);
      // Remove the znodes we've already tried from this list
      serverHosts.removeAll(connParams.getRejectedHostZnodePaths());
      if (serverHosts.isEmpty()) {
        throw new ZooKeeperHiveClientException(
            "Tried all existing HiveServer2 uris from ZooKeeper.");
      }
      // Now pick a server node randomly
      serverNode = serverHosts.get(randomizer.nextInt(serverHosts.size()));
      connParams.setCurrentHostZnodePath(serverNode);
      // Read data from the znode for this server node
      // This data could be either config string (new releases) or server end
      // point (old releases)
      String dataStr =
          new String(
              zooKeeperClient.getData().forPath("/" + zooKeeperNamespace + "/" + serverNode),
              Charset.forName("UTF-8"));
      Matcher matcher = kvPattern.matcher(dataStr);
      // If dataStr is not null and dataStr is not a KV pattern,
      // it must be the server uri added by an older version HS2
      if ((dataStr != null) && (!matcher.find())) {
        String[] split = dataStr.split(":");
        if (split.length != 2) {
          throw new ZooKeeperHiveClientException("Unable to read HiveServer2 uri from ZooKeeper: "
              + dataStr);
        }
        connParams.setHost(split[0]);
        connParams.setPort(Integer.parseInt(split[1]));
      } else {
        applyConfs(dataStr, connParams);
      }
    } catch (Exception e) {
      throw new ZooKeeperHiveClientException("Unable to read HiveServer2 configs from ZooKeeper", e);
    } finally {
      // Close the client connection with ZooKeeper
      if (zooKeeperClient != null) {
        zooKeeperClient.close();
      }
    }
  }
```