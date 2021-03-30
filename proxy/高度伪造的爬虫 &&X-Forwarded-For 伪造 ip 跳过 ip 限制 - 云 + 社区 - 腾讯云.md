> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [cloud.tencent.com](https://cloud.tencent.com/developer/article/1544628)

针对需要大量代理 ip 的 R×× 项目，采用伪造式的请求头跳过验证码和每日请求次数限制，现在针对请求做详细的拟人化，让对面更难以察觉。如有不足多多指教。项目最新完整代码放在 github 上：因为目前正在运作项目完结后公开，下文中有可运行代码

*   总结一下：
*   1：user—agent ： 采用万行的 user 列表，每次随机使用，伪造浏览器以及屏幕和系统等信息
*   2：cookie ： 带真实 cookie
*   3：任务队列 ： 完全打散
*   4：伪造 ip 队列 ： 一个伪造 ip 使用 1-4 次随机值，ip 本身使用美国的 isp 以及基准点和抓取到的是代理的 ip
*   5：修改 refroad 修改上次来源页面。选择搜索引擎，首页， 同级别页面跳转
*   6：随机休息时间，1-10 秒，波浪化抓取时间白天黑夜分开，拟人。
*   7：限制抓取速度，设定抓取优先级优先爬取活跃部分
*   8：大招：代理 / 多机器 + xfor 伪造。需要数百个稳定的可用代理或者 V** / 需要多台机器。
*   9：采集浏览器请求信息，加入模拟表中

1：user—agent ：

```
useragents = []
useragentsock = open(os.path.basename("user_agent.txt"),"r")
for record in useragentsock:
    useragents.append(record.rstrip("\n"))
useragentsock.close()
 
useragent = random.choice(useragents)

```

3：完全打散队列：

```
#coding:utf-8
import os,re,time,MySQLdb,MySQLdb.cursors,urllib2,random
from redis import Redis
 
#r = Redis()
r = Redis(host='ip', port=6379, db=0)
 
MYSQL_HOST = 'ip'
MYSQL_DBNAME = 'name'
MYSQL_USER = 'user'
MYSQL_PASSWD = 'mima'
MYSQL_PORT=33306
 
conn = MySQLdb.connect(host=MYSQL_HOST,user=MYSQL_USER,passwd=MYSQL_PASSWD,db=MYSQL_DBNAME,port=MYSQL_PORT)
cur=conn.cursor()
cur.execute('SELECT inet_ntoa(minip),inet_ntoa(maxip) FROM Common.IP2Location_Dharmsala;')
#cur.execute(‘select * from as_rtb limit 10;’)
b=[]
for data in cur.fetchall():
    date_0 = data[0].split('.')
    print data[0].split('.')[3],data[1].split('.')[3]
    for i  in (range(int(data[0].split('.')[3]),int(data[1].split('.')[3])+1)):
        ip = date_0[0]+'.'+date_0[1]+'.'+date_0[2]+'.'+str(i)
        b.append(ip)
a =  (random.sample(b,len(b)))
 
for i in a :
    r.lpush('ip',i)
 
print r.llen('ip')
cur.close()
conn.close()

```

4：伪造 ip 队列 ：

本部分在另外一个 py 文件中，需要 forword_list.txt 列表，1-4 次的随机值在主程序中控制，此代码只负责加入队列

```
import os
import random
from redis import Redis
r = Redis()
 
print r.keys('*')
 
forword_list= []
sock = open(os.path.basename("forword_list.txt"),"r")
for record in sock:
    forword_list.append(record.rstrip("\n"))
sock.close()
#print forword_list
 
for i in range(20000):
    r.lpush('forword',random.choice(forword_list))
 
print r.llen('forword')

```

5：修改 refroad 修改上次来源页面：

```
Referers =['https://ip.rtbasia.com/','https://ip.rtbasia.com/','https://www.baidu.com/',
'https://www.google.com/','https://www.hao123.com/','http://www.sogou.com']
 
referer = random.choice(Referers)

```

6：随机休息时间。根据白天黑夜变化

```
 time_shi = str(time.ctime()).split(' ')[3].split(':')[0]
 if(time_shi > 6 and time_shi < 22):
 time_sleep = int(random.random()*10)
 else:
 time_sleep = int(random.random()*20)
 time.sleep(time_sleep)

```

9：最终请求头为：

```
    i_headers = {
        'User-Agent':useragent,
        "X-Forwarded-For": forward,
        "Referer":referer,
 
        "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
        "Accept-Encoding":"gzip, deflate, sdch",
        "Accept-Language":"zh-CN,zh;q=0.8",
        "Cache-Control":"max-age=0",
        "Connection":"keep-alive",
        "Cookie":"JSESSIONID=1BB136DED100C9853939FE8017BE6BC9; _ga=GA1.2.1225973112.1470798799",
        "Host":"ip.rtbasia.com",
        "Upgrade-Insecure-Requests":"1"
    }

```

目前源代码如下：不可直接使用，需要根据自己的项目进行修改，主要得学习一下这种伪造思路

```
#coding:utf-8
import os,re,time,MySQLdb,MySQLdb.cursors,urllib2,random
from redis import Redis
#setting
#r = Redis()
r = Redis(host='ip', port=6379, db=0)
 
MYSQL_HOST = 'ip'
MYSQL_DBNAME = 'db'
MYSQL_USER = 'user'
MYSQL_PASSWD = 'mima'
MYSQL_PORT=33306
 
#init
useragents = []
useragentsock = open("./user_agent.txt","r")
for record in useragentsock:
    useragents.append(record.rstrip("\r\n"))
useragentsock.close()
 
Referers =['https://ip.rtbasia.com/','https://ip.rtbasia.com/','https://www.baidu.com/',
'https://www.google.com/','https://www.hao123.com/','http://www.sogou.com']
 
#请求伪造部分
def url_user_agent(url,forward,referer):
    useragent = random.choice(useragents)
    print useragent
    i_headers = {
        'User-Agent':useragent,
        "X-Forwarded-For": forward,
        "Referer":referer,
        "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
        "Accept-Encoding":"gzip, deflate, sdch",
        "Accept-Language":"zh-CN,zh;q=0.8",
        "Cache-Control":"max-age=0",
        "Connection":"keep-alive",
        "Cookie":"JSESSIONID=1BB136DED100C9853939FE8017BE6BC9; _ga=GA1.2.1225973112.1470798799",
        "Host":"ip.rtbasia.com",
        "Upgrade-Insecure-Requests":"1"
    }
    req = urllib2.Request(url,headers=i_headers)
    doc = urllib2.urlopen(req).read()
    return doc
 
#main
while(1):
    #队列自动补给
    if(r.llen('ip') < 10 ):
        print "任务ip队列已经空了"
        time.sleep(100)
        continue#跳过后面所有
    else:
        print "任务开始：当前任务队列长度为:",r.llen('ip')
    if(r.llen('forword') < 100):
        print "伪造forward队列不足100，正在重新加入队列"
        forword_list= []
        sock = open("./forword_list.txt","r")
        for record in sock:
            forword_list.append(record.rstrip("\n"))
        sock.close()
        for i in range(20000):
            r.lpush('forword',random.choice(forword_list))
        print "forword现在长度为:",r.llen('forword')
 
    referer = random.choice(Referers)
    forward = str(r.lpop('forword'))
    temp_cishu =  int(random.random()*5)
    print "referer & forward: ",referer,forward
 
    #在4次内，随机值，让同一个useragenthe referer 并不只是访问一次
    while(temp_cishu):
        temp_cishu -= 1
        conn = MySQLdb.connect(host=MYSQL_HOST,user=MYSQL_USER,passwd=MYSQL_PASSWD,db=MYSQL_DBNAME,port=MYSQL_PORT)
        cur=conn.cursor()
        ip = str(r.lpop('ip'))
        temp_url = 'https://ip.rtbasia.com/map/ip?ip=' + ip
        print temp_url
        #发起请求
        str_body  = url_user_agent(temp_url,forward,referer)
        #以下部分采用正则匹配并存储数据库
        fail = re.findall(r'Status: fail',str_body)
        if(len(fail) == 0):#没有匹配到fail则说明成功，其len长度为0
            print "成功获取到网页源码"
            yanzheng = re.findall(r'验证',str_body)
            if(len(yanzheng) == 0):
                temp_data = re.findall('\d{1,3}.\d{10,50},\s?\d{1,3}.\d{10,50}',str_body)
                #print temp_data
                if(temp_data == []):
                    print '未捕捉到数据'
                    article_name = ''
                else:
                    temp_data.remove(temp_data[0])
                    article_name = str(temp_data)
                    print ip,article_name
                #数据库部分
                try:
                    cur.execute('''
                        INSERT INTO rtb(`ip`, `longitude_latitude`)
                        VALUES (%s,%s)
                    ''' , (ip,article_name)
                    )
                    conn.commit()
                except MySQLdb.Error,e:
                    print "Mysql Error %d: %s" % (e.args[0], e.args[1])
 
            else:
                print "出现验证，将该网址重新加入队列"
                r.lpush('ip',ip)
        else:
            print "访问出错，重新将ip放入队列"
            r.lpush('ip',ip)
 
        cur.close()
        conn.close()
 
        #波浪化sleep时间间隔
        time_shi = int(str(time.ctime()).split(' ')[3].split(':')[0])
        print time_shi
        if( time_shi >= 6 and time_shi <= 22 ):
            time_sleep = int(random.random()*10)
        else:
            time_sleep = int(random.random()*20)
        print "请等待: ",time_sleep
        time.sleep(time_sleep)
        print
        print '---×××+×××----华丽丽的分界线---×××+×××----'
        print

```

_**原创文章，转载请注明：** 转载自_ [_URl-team_](https://www.urlteam.cn/)

_**本文链接地址:**_ [_高度伪造的爬虫 &&X-Forwarded-For 伪造 ip 跳过 ip 限制_](https://www.urlteam.cn/2016/08/%e4%bb%bf%e4%ba%ba%e5%8c%96%e4%bc%aa%e9%80%a0%e8%af%b7%e6%b1%82%e5%a4%b4/)

No related posts.