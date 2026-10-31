# skyatc-model

SkyATC 的离线语音识别模型，单独放在这个仓库里。

## 为什么单独开一个仓库

SkyATC 主仓库现在是私有的，而 GitHub 免费账户的 Pages 只对公开仓库开放——主仓库一旦私有，`core-cz.github.io/skyatc` 就会失效，模型也就下不来了。

所以把模型拆出来，放这个公开仓库，Pages 单独开一份。主仓库私有、站点部署在 Cloudflare Pages、模型从这里的 Pages 走，三件事互不牵连。

## 文件

| 路径 | 大小 | 说明 |
| --- | --- | --- |
| `vosk/model.tar.gz` | 41,206,109 字节 | Vosk 英文模型（Kaldi WASM 用），解压前约 39 MB |

SHA-256：

```
6b4d79ca2d9fc7a6a3284325773dd0e151f12d42f853e6993b63c074dde16825
```

## 访问地址

```
https://core-cz.github.io/skyatc-model/vosk/model.tar.gz
```

前端在 `src/lib/asr.ts` 的 `MODEL_SOURCES` 里引用这个地址，按下面的顺序回退：

```
VITE_MODEL_URL（自建 CDN）→ 同源 ./vosk/ → 本仓库 Pages
```

模型是静态资源，Content-Type 由 Pages 按扩展名给（`application/gzip`），前端不看 Content-Type，只按字节数判断——小于 8 MB 的响应一律当成「路由回退返回的 HTML」丢掉。

## 换模型时

1. 替换 `vosk/model.tar.gz`，更新本文件的字节数和 SHA-256；
2. 同步改 `src/lib/asr.ts` 里的 `MODEL_BYTES_HINT`；
3. `MODEL_CACHE_KEY` 是固定值（`https://skyatc.local/vosk/model.tar.gz`），**不要动**——它跟来源解耦，换了托管地址不会让用户白下 39 MB。但如果模型内容本身换了，得升 `CACHE_NAME` 的版本号，否则老用户会一直用缓存里的旧模型。

## 许可

模型来自 [Vosk](https://alphacephei.com/vosk/) 的英文通用模型（Apache-2.0）。
