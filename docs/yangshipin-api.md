# 央视频官方 API 说明

央视频（中央广播电视总台官方 App）的直播接口，可获取 CCTV 全套 + 22 家卫视的官方直播流。

## 接口

```
POST https://player-api.yangshipin.cn/v1/player/get_live_info
```

## 请求头

```
Content-Type: application/json
Referer: https://www.yangshipin.cn/
Cookie: guid=0;vplatform=109
Yspappid: 519748109
```

## 请求体（JSON）

```json
{
  "adjust": 1,
  "appVer": "app_version=V1.0.0",
  "cKey": "<AES-128-CBC 加密结果>",
  "channel": "ysp_tx",
  "cmd": 2,
  "cnlid": "<频道ID>",
  "defn": "fhd",
  "devid": "devid",
  "dtype": 1,
  "encryptVer": "8.1",
  "guid": "0",
  "otype": "ojson",
  "platform": "5910204",
  "rand_str": "<当前unix时间戳>",
  "sphttps": 1,
  "stream": 2,
  "signature": "<MD5签名>"
}
```

- `defn`: `fhd` = 1080p
- `cnlid`: 频道 ID，见下方对照表

## 签名算法

### cKey（AES-128-CBC）

1. 构造字符串：
   ```
   |{cnlid}|{time}|mg3c3b04ba|V1.0.0|{guid}|{platform}|https://www.yangshipin.c|mozilla/5.0 (windows nt ||Mozilla|Netscape|Win32|
   ```
2. 前置 Java 风格 32-bit string hashCode
3. AES-128-CBC 加密：
   - key (hex): `48e5918a74ae21c972b90cce8af6c8be`
   - iv (hex): `9a7e7d23610266b1d9fbf98581384d92`
4. `cKey = "--01" + 大写十六进制(ciphertext)`

### signature（MD5）

```
signature = md5(http_build_query(全部字段) + "0f$IVHi9Qno?G")
```

## 响应

```json
{"code":0,"data":{"playurl":"<直接 m3u8 地址>"}}
```

- `code` 非 0 时重试（实现中最多重试 10 次）
- 返回的 m3u8 为签名地址，有效期短，每次播放前重新获取

## 限制

- 仅限中国大陆 IP
- 签名地址有时效性，不可硬编码

## CCTV 频道 ID（cnlid）

| 频道 | cnlid | 频道 | cnlid |
|---|---|---|---|
| CCTV-1 综合 | 2000210103 | CCTV-10 科教 | 2000203503 |
| CCTV-2 财经 | 2000203603 | CCTV-11 戏曲 | 2000204103 |
| CCTV-3 综艺 | 2000203803 | CCTV-12 社会与法 | 2000202603 |
| CCTV-4 中文国际 | 2000204803 | CCTV-13 新闻 | 2000204603 |
| CCTV-5 体育 | 2000205103 | CCTV-14 少儿 | 2000204403 |
| CCTV-5+ 体育赛事 | 2000204503 | CCTV-15 音乐 | 2000205003 |
| CCTV-6 电影 | 2000203303 | CCTV-16 奥林匹克 | 2012375003 |
| CCTV-7 国防军事 | 2000510003 | CCTV-17 农业农村 | 2000204203 |
| CCTV-8 电视剧 | 2000203903 | CCTV-4K | 2000266303 |
| CCTV-9 纪录 | 2000499403 | CCTV-16 4K | 2012492303 |
| CGTN 英语 | 2001656803 | CCTV-8K | 2020603401 |
| CGTN 西语 | 2010152503 | CGTN 纪录 | 2010155403 |
| CGTN 法语 | 2010153503 | CGTN 阿语 | 2010155203 |
| CGTN 俄语 | 2010152603 | | |

另有 CCTV 兵器科技/第一剧场/怀旧剧场/风云剧场/风云音乐/风云足球/电视指南/女性时尚/央视文化精品/世界地理/高尔夫网球/央视台球/卫生健康等专题频道。

## 参考

- https://github.com/xiongjian83/TvBox/blob/main/ysp.php（接口实现与完整频道表）
