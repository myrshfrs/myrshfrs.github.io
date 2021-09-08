---
layout: default
date: 2021-09-07
---

## 怎么让telegram的用户名动态显示emoji形式的时间？

想想，telegram的用户名能够显示emoji形式的现在时间，是不是很酷炫。我之前在telegram上看见有人的用户名可以显示现在的时间，就从网上搜了搜怎么能够实现这个功能的教程，后面自己做了一些改进，现在可以显示emoji形式的时间了，实现起来并不复杂，一个python小脚本就可以了。最终效果如下图。

![username_with_emoji_time](https://images2.imgbox.com/4b/9f/MxCZgbGK_o.png)

为了保证脚本能稳定运行，建议安装在vps上。我是用debian 10系统的一个vps运行的。下面介绍安装使用的方法。

首先安装python3，创建一个python3的虚拟环境，并使用pip安装脚本运行需要的几个模块。


```bash
cd /home/zhangwf/
apt update -y && apt upgrade -y
apt-get install python3 python3-venv -y
python3 -m venv tgname
cd /home/zhangwf/tgname/
bin/pip install --upgrade pip
bin/pip install telethon emoji pytz

```

下面是python脚本，你可以根据自己的需要，对脚本内容进行修改。

```python
from telethon import TelegramClient
from telethon.tl.functions.account import UpdateProfileRequest
import asyncio, pytz, emoji
from datetime import datetime
from dateutil.tz import tzutc
import inflect

api_id = your_api_id
api_hash = 'your_api_bash'
client = TelegramClient('session_name', api_id, api_hash)
client.start()
a = [0, 1, 2, 3, 4, 5, 6, 7]
async def main():
    while True:
        local_timezone = pytz.timezone('Asia/Shanghai')
        utc_datetime = datetime.now(tzutc())
        local_datetime = utc_datetime.astimezone(local_timezone)
        time = local_datetime.strftime("%Y/%m/%d %a %H:%M %p")
        p = inflect.engine()
        a[0] = p.number_to_words(int(time[15]))
        a[1] = p.number_to_words(int(time[16]))
        a[2] = p.number_to_words(int(time[18]))
        a[3] = p.number_to_words(int(time[19]))
        a[4] = p.number_to_words(int(time[5]))
        a[5] = p.number_to_words(int(time[6]))
        a[6] = p.number_to_words(int(time[8]))
        a[7] = p.number_to_words(int(time[9]))
        emoji_date = emoji.emojize(':'+a[4]+'::'+a[5]+':.:'+a[6]+'::'+a[7]+':', use_aliases=True)
        emoji_time = emoji.emojize(':'+a[0]+'::'+a[1]+':::'+a[2]+'::'+a[3]+':', use_aliases=True)
        emoji_pm = emoji.emojize(':sunrise_over_mountains:', use_aliases=True)
        emoji_am = emoji.emojize(':sunrise:', use_aliases=True)
        async with client:
            if 'PM' in time:
                await client(UpdateProfileRequest(last_name = emoji_date + ' ' + emoji_pm + ' ' + emoji_time))
                await asyncio.sleep(60)
            if 'AM' in time:
                await client(UpdateProfileRequest(last_name = emoji_date + ' ' + emoji_am + ' ' + emoji_time))
                await asyncio.sleep(60)
asyncio.get_event_loop().run_until_complete(main())

```

注意，你需要把脚本里的api_id和api_hash换成自己的。详情见[怎么申请api](https://core.telegram.org/api/obtaining_api_id)。

然后，手动运行一下脚本，看看有没有错误，没有错误的话第一次运行需要登录你的telegram账号。输入手机号码的时候注意带上国际电话区号。比如：+86133XXXXXXXX。然后输入其他客户端或者手机短信收到的code。登录成功之后我们就可以把脚本关掉了，然后用systemd使它保持开机自启和后台运行。

```
cat>/etc/systemd/system/changetelegramname@.service<<EOF
[Unit]
Description=change telegram name with time
After=network.target
[Service]
User=zhangwf
Type=simple
WorkingDirectory=/home/zhangwf/tgname
ExecStart=/usr/bin/bash -c '/home/zhangwf/tgname/bin/python3 /home/zwf/flexget/changetelegramname.py'
ExecStop=/usr/bin/bash -c 'ps -ef | grep changetele | awk '{print $2}' | xargs kill -9'
ExecReload=/usr/bin/bash -c 'ps -ef | grep changetele | awk '{print $2}' | xargs kill -9  && /home/zhangwf/tgname/bin/python3 /home/zhangwf/tgname/changetelegramname.py'
Nice=19
restart=always
RestartSec=3
TimeoutStopSec=10
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable changetelegramname@zhangwf
systemctl start changetelegramname@zhangwf
systemctl status changetelegramname@zhangwf
```

打开电脑或手机上的telegram，看看自己的名字会不会随时间变化吧。

[back](./)
