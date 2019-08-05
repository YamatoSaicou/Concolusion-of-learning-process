由于在研究生毕业设计上需要在demo中插入比较专业的英语发音，经过一番选择后决定使用Microsoft Azure TTS API使用来完成。<br>
（其他的文字转语音api要么免费额度太少，要么生成后剪辑不是很方便，于是决定自己搞）

研究了一番微软的文档后，总结了主要使用方法是：

1. 现在Microsoft Azure TTS API开发平台拿到自己的key（免费5000条语音，足够了）。
2. 按照官方文档构建文本转语音的类，配置好类方法，将自己的key填入（理论上讲是需要环境变量导入，不过自己使用的话直接把key粘进去即可）。
3. 根据需求修改类元素(文本),run!

```python
import os, requests, time
from xml.etree import ElementTree  #缺啥就pip install

subscription_key = "Your-Key-Goes-Here"  #把申请的key放到这里

class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
       # self.tts = input("What would you like to convert to speech: ")
        self.tts = "You can click her to look over her eating log and give your suggestion about it."  #想要转语音的文本在这里
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None

    '''
    The TTS endpoint requires an access token. This method exchanges your
    subscription key for an access token that is valid for ten minutes.
    '''
    def get_token(self):
        fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
        headers = {
            'Ocp-Apim-Subscription-Key': self.subscription_key
        }
        response = requests.post(fetch_token_url, headers=headers)
        self.access_token = str(response.text)

    def save_audio(self):
        base_url = 'https://westus.tts.speech.microsoft.com/'
        path = 'cognitiveservices/v1'
        constructed_url = base_url + path
        headers = {
            'Authorization': 'Bearer ' + self.access_token,
            'Content-Type': 'application/ssml+xml',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'User-Agent': 'YOUR_RESOURCE_NAME'
        }
        xml_body = ElementTree.Element('speak', version='1.0')
        xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
        voice = ElementTree.SubElement(xml_body, 'voice')
        voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
        voice.set('name', 'en-US-Guy24kRUS')
        voice.text = self.tts
        body = ElementTree.tostring(xml_body)

        response = requests.post(constructed_url, headers=headers, data=body)
        if response.status_code == 200:
            with open('sample-' + self.timestr + '.wav', 'wb') as audio:
                audio.write(response.content)
                print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
        else:
            print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")

if __name__ == "__main__":
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```
