<img width="3024" height="4032" alt="5c152a76897496e362a206b9fc050612" src="https://github.com/user-attachments/assets/e4d90cab-9aa7-406f-9629-a0ec2f9e30a9" />
文档链接：
https://docs.coingecko.com/reference/simple-price

我让AI帮我理解了什么：
让 AI（DeepSeek）帮我解读 CoinGecko 的 API 文档，搞清楚怎么通过 simple/price 接口实时查询 ETH 的美元价格。我特别要求它解释清楚：vs_currency 参数怎么填、返回的 JSON 数据长什么样、以及 API 调用是否需要 Key。

AI生成了什么代码骨架/技术方案：
AI 生成了一个 Python 最小调用脚本（骨架），用于获取 ETH 实时价格：

```python
import requests

url = "https://api.coingecko.com/api/v3/simple/price?ids=ethereum&vs_currencies=usd"
response = requests.get(url)
data = response.json()
print(data)
我手动改了什么（重点！体现了人工判断）：
加了错误处理：AI 没写 try-except，我手动加了网络超时和状态码判断（因为 Ops 搞活动演示时最怕报错冷场）。
改了取值逻辑：AI 直接 print (data) 太粗糙，我手动改成 eth_price = data ['ethereum']['usd']，方便后续复用。
加了延迟：AI 没提醒免费版有调用频率限制，我手动加了 time.sleep (2) 防止被 Ban（这是我上次踩坑 “余额不足” 连带学到的）。
import requests
import time

time.sleep(2)  # 手动加的限频保护
url = "https://api.coingecko.com/api/v3/simple/price?ids=ethereum&vs_currencies=usd"

try:
    response = requests.get(url, timeout=10)
    if response.status_code == 200:
        data = response.json()
        eth_price = data['ethereum']['usd']
        print(f"当前 ETH 价格: ${eth_price}")
    else:
        print(f"请求失败，状态码：{response.status_code}")
except Exception as e:
    print(f"网络报错：{e}")
