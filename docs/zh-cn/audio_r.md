# 接口声明

# 声纹注册
---
导入音频声纹注册入库，返回声纹ID.

    请求基路径：[服务器IP]:8100/v1/

### 基本信息
* 请求url：vpr/register

* 请求协议：HTTP

* 请求方式：POST

* 请求格式：multipart/form-data

* 响应格式：application/json


* 请求参数

| 字段    | 参数位置     | 类型     | 默认值 | 必输项 | 结构 | 描述      |
|-------|----------|--------|-----|-----|----|---------|
| group | formData | string |     | 是   |可以为空字符串    | 声纹自定义分组 |
| train | formData | file   |     | 是   |    |  待注册音频     |

* 返回参数
``` json
{
    'hasError': False,
    'errorId': '',
    'errorDesc': '',
    'data': {'id': '25526fd1-7053-4c65-b94e-09d71b079ef9'}   //返回音频ID
}  
```

### 测试用例（python）

```python
import requests
import json
def get_file_name(file_path):
    """获取文件名"""
    return file_path.split(r'/')[-1]

def read_audio(audio_path):
    """读取音频"""
    with open(audio_path, 'rb') as f:
        audio_content = f.read()
    return audio_content

def register(audio_path, group='ceshi'):
    """声纹注册"""
    url = "http://192.168.0.1:8100/v1/vpr/register"
    headers = {
        'appid': 'speakin'
    }
    audio_name = get_file_name(audio_path)
    data = {'group': group}
    files = [('train', (audio_name, read_audio(audio_path)))]
    res = requests.post(url, data=data, files=files, headers=headers).json()
    return res

def main():
    # 声纹路径和声纹名称
    audio_path = '/tmp/audio/1.wav'
    audio_name = get_file_name(audio_path)
    # 请求头
    res = register(audio_path)
    print(res)


if __name__ == "__main__":
    main()
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---
# 音频-声纹比对1:n
音频和注册过的声纹进行1:n对比，返回对比分数

    请求基路径：[服务器IP]:8100/v1/

### 基本信息
* 请求url：/vpr/compare_with_id_n

* 请求协议：HTTP

* 请求方式：POST

* 请求格式：multipart/form-data

* 响应格式：application/json

* 请求参数

| 字段    | 参数位置     | 类型     | 默认值 | 必输项 | 结构 | 描述      |
|-------|----------|--------|-----|-----|----|---------|
| group | formData | string |     | 是   |可以为空字符串  | 要比对的声纹所属分组 |
| train | formData | file   |     | 是   |    |         |
| test_ids | formData | string   |     | 是   |    |   要比对的声纹id，可以有若干个    |

* 返回参数
``` json
{
	'hasError': False,
	'errorId': '',
	'errorDesc': '',
	'data': {
		'cmpitems': [{
			'id': 'd3757eef-0006-4f09-a2a9-61f30b432177', // 声纹id
			'score': 132.01208 //比对分数
		}, {
			'id': 'feab3ba5-d75e-4256-a4eb-25779b32179b',
			'score': 122.239685
		}]
	}
}
```

### 测试用例（python）

```python
import requests
import json
def get_file_name(file_path):
    """获取文件名"""
    return file_path.split(r'/')[-1]

def read_audio(audio_path):
    """读取音频"""
    with open(audio_path, 'rb') as f:
        audio_content = f.read()
    return audio_content

def register(audio_path, group='ceshi'):
    """声纹注册"""
    url = "http://192.168.0.1:8100/v1/vpr/register"
    headers = {
        'appid': 'speakin'
    }
    audio_name = get_file_name(audio_path)
    data = {'group': group}
    files = [('train', (audio_name, read_audio(audio_path)))]
    res = requests.post(url, data=data, files=files, headers=headers).json()
    return res

def compare_with_id_n(train_audio_path, test_ids, group='ceshi'):
    """音频-声纹比对1:n"""
    headers = {
        'appid': 'speakin'
    }
    url = 'http://192.168.0.25:8100/v1/vpr/compare_with_id_n'
    train_audio_name = get_file_name(train_audio_path)
    data = {
        'test_ids': test_ids,
        'group': group
    }
    files = [(train_audio_name, read_audio(train_audio_path))]
    res = requests.post(url, data, files, headers=headers).json()
    return res

def main():
    """音频-声纹比对1:n"""
    train_audio_path = 'audio/1.wav'
    test_audio_paths = ['audio/2.wav', 'audio/3.wav']
    test_audio_ids = []
    for test_audio_path in test_audio_paths:
        register_res = register(test_audio_path)
        test_audio_id = register_res.get('data').get('id')
        test_audio_ids.append(test_audio_id)
    res = compare_with_id_n(train_audio_path, test_audio_ids)
    print(res)


if __name__ == "__main__":
    main()
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
