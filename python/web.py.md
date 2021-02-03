一、web.py的开发(web.py的官网：http://webpy.org)
		  1、python的安装:
		  2、web.py的安装：pip install web.py
		                   pip list(显示所有安装)
	 	  3、pip的测试        

```shell
				import web
				urls = (
				    '/(.*)', 'hello'
				)
				app = web.application(urls, globals())

				class hello:        
				    def GET(self, name):
				        if not name: 
				            name = 'World'
				        return 'Hello, ' + name + '!'

				if __name__ == "__main__":
				    app.run()
       注意：要开启防火墙(注意格式的对齐)
		     sulei@sulei-virtual-machine:~/python/workspace$ python helloweb.py 
		     http://0.0.0.0:8080/
       
       -URL映射
        -URL完全匹配(/index)
        -URL模糊匹配(/post/\d+)
        -URL带组匹配(/post/(\d+))
        区别
        百度???案列(hello.py)
      
      4、web.py的请求流程
         -请求参数获取
          -web.input()
         -请求头获取
          -web.ctx.env
      5、响应处理
         -模板文件读取
          render.index(“参数”)
         -结果数据获取
          -model.select("sql")
         -URL跳转
           -web.seeother("/")
      4、第一个完整demo
```