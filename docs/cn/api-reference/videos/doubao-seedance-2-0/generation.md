# Seedance 2.0 视频生成
Source: https://docs.toapis.com/docs/cn/api-reference/videos/doubao-seedance-2-0/generation

POST https://toapis.com/v1/videos/generations
使用字节跳动 Seedance 2.0 / Seedance 2.0 Fast 模型生成视频

* 字节跳动新一代视频生成模型
* 支持 `doubao-seedance-2-0` 与 `doubao-seedance-2-0-fast`
* 支持文生视频、首帧图生视频、首尾帧图生视频、多模态参考生视频
* 支持参考图、参考视频、参考音频联合控制
* 支持生成同步音频、联网搜索工具、返回尾帧图
* 异步任务管理，通过任务 ID 查询结果

## Authorizations

<ParamField type="string">
  所有接口均需要使用 Bearer Token 进行认证

  获取 API Key：访问 [API Key 管理页面](https://toapis.com/console/token) 获取您的 API Key

  使用时在请求头中添加：

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Body

<ParamField type="string">
  视频生成模型名称

  可用模型：

  * `doubao-seedance-2-0` - 标准版，追求更高生成质量，时长支持 `4-15` 秒
  * `doubao-seedance-2-0-fast` - 极速版，适合快速预览与迭代，时长支持 `4-12` 秒
</ParamField>

<ParamField type="string">
  视频内容描述

  支持中英文输入。建议明确描述场景、镜头运动、主体动作、风格和声音氛围。

  建议：

  * 中文尽量控制在 `500` 字以内
  * 英文尽量控制在 `1000` 词以内
  * 需要引用参考素材时，使用“图片1 / 视频1 / 音频1”的方式指代

  示例：`"全程使用视频1的第一视角构图，首帧参考图片1，尾帧参考图片2，保留音频1的节奏与氛围"`
</ParamField>

<ParamField type="integer">
  视频时长（秒）

  取值规则：

  * `doubao-seedance-2-0`：`4-15`
  * `doubao-seedance-2-0-fast`：`4-12`
  * `-1`：自动时长，由模型自行决定

  <Warning>
    `doubao-seedance-2-0-fast` 不支持超过 `12` 秒的视频时长。
  </Warning>
</ParamField>

<ParamField type="string">
  视频宽高比

  可选项：

  * `21:9`
  * `16:9`
  * `4:3`
  * `1:1`
  * `3:4`
  * `9:16`
  * `adaptive`

  `adaptive` 适配规则：

  * 文生视频：模型根据提示词自动选择最合适的比例
  * 首帧或首尾帧图生视频：根据首帧图片自动适配
  * 多模态参考生视频：通常优先参考视频，其次参考图片
</ParamField>

<ParamField type="string[]">
  兼容模式下的图片 URL 数组

  建议优先使用 `image_with_roles` 以获得更清晰的角色控制。

  `image_urls` 与 `image_with_roles` 不建议同时使用。
</ParamField>

<ParamField type="array">
  带角色的图片数组

  支持场景：

  * 首帧图生视频：`first_frame` 1 张
  * 首尾帧图生视频：`first_frame` 1 张 + `last_frame` 1 张
  * 多模态参考生视频：`reference_image` 1-9 张

  <Expandable title="字段说明">
    <ParamField type="string">
      图片 URL、Base64 数据或已上传素材 URI

      支持：

      * `https://...`
      * `data:image/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField type="string">
      图片角色

      可选项：

      * `first_frame` - 首帧图
      * `last_frame` - 尾帧图
      * `reference_image` - 参考图
    </ParamField>
  </Expandable>

  图片要求：

  * 格式：`jpeg`、`png`、`webp`、`bmp`、`tiff`、`gif`
  * 单张大小：小于 `30MB`
  * 整体请求体：建议不超过 `64MB`
  * 宽高比：约 `0.4` 到 `2.5`
  * 宽高尺寸：约 `300px` 到 `6000px`

  <Warning>
    - 首帧/首尾帧模式与多模态参考模式互斥，不能和 `reference_image` / `reference_video` / `reference_audio` 混用
    - `first_frame` 最多 1 张，`last_frame` 最多 1 张
    - 多模态参考模式下，所有图片都应使用 `reference_image`
  </Warning>
</ParamField>

<ParamField type="array">
  带角色的视频数组

  当前仅支持多模态参考模式使用 `reference_video`。

  <Expandable title="字段说明">
    <ParamField type="string">
      视频 URL 或已上传素材 URI

      支持：

      * `https://...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField type="string">
      固定为：

      * `reference_video`
    </ParamField>
  </Expandable>

  视频要求：

  * 格式：`mp4`、`mov`
  * 分辨率：`480p` 或 `720p`
  * 单条时长：`2-15` 秒
  * 最多：`3` 条参考视频
  * 所有参考视频总时长：不超过 `15` 秒
  * 单条大小：小于 `50MB`
  * 帧率：约 `24-60 FPS`
</ParamField>

<ParamField type="array">
  带角色的音频数组

  当前仅支持多模态参考模式使用 `reference_audio`。

  <Expandable title="字段说明">
    <ParamField type="string">
      音频 URL、Base64 数据或已上传素材 URI

      支持：

      * `https://...`
      * `data:audio/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField type="string">
      固定为：

      * `reference_audio`
    </ParamField>
  </Expandable>

  音频要求：

  * 格式：`wav`、`mp3`
  * 单条时长：`2-15` 秒
  * 最多：`3` 段参考音频
  * 所有参考音频总时长：不超过 `15` 秒
  * 单条大小：小于 `15MB`

  <Warning>
    `audio_with_roles` 不能单独使用，至少还需要一个图片或视频参考输入。
  </Warning>
</ParamField>

<ParamField type="object">
  扩展参数

  <Expandable title="字段说明">
    <ParamField type="string">
      视频分辨率

      可选项：

      * `480p`
      * `720p`
    </ParamField>

    <ParamField type="boolean">
      是否生成同步音频

      * `true`：生成带音频的视频
      * `false`：生成无声视频

      模型会根据文本与视觉内容自动生成对白、音效和背景音乐。
    </ParamField>

    <ParamField type="boolean">
      是否在结果中额外返回尾帧图片
    </ParamField>

    <ParamField type="array">
      模型工具配置

      当前仅支持：

      * `[{ "type": "web_search" }]`

      说明：

      * 仅建议用于文生视频
      * 开启后模型会按需搜索，通常会提升时效性，但也会增加时延
    </ParamField>

    <ParamField type="integer">
      随机种子，用于控制生成随机性
    </ParamField>
  </Expandable>
</ParamField>

## 输入组合规则

支持的典型输入组合：

* 纯文本：文生视频
* 文本 + 1 张首帧图：首帧图生视频
* 文本 + 首帧图 + 尾帧图：首尾帧图生视频
* 文本 + 参考图：多模态参考生视频
* 文本 + 参考视频：视频参考生视频
* 文本 + 参考图 + 参考音频：多模态参考生视频
* 文本 + 参考图 + 参考视频 + 参考音频：多模态参考生视频

<Warning>
  三种模式互斥：

  * 首帧图生视频
  * 首尾帧图生视频
  * 多模态参考生视频

  如果你需要严格控制首帧和尾帧，请优先使用 `first_frame` / `last_frame`；如果更看重综合参考能力，请使用 `reference_image` / `reference_video` / `reference_audio`。
</Warning>

## 分辨率与宽高比像素映射

| 分辨率    | 宽高比    | 像素值        |
| ------ | ------ | ---------- |
| `480p` | `16:9` | `864x496`  |
| `480p` | `4:3`  | `752x560`  |
| `480p` | `1:1`  | `640x640`  |
| `480p` | `3:4`  | `560x752`  |
| `480p` | `9:16` | `496x864`  |
| `480p` | `21:9` | `992x432`  |
| `720p` | `16:9` | `1280x720` |
| `720p` | `4:3`  | `1112x834` |
| `720p` | `1:1`  | `960x960`  |
| `720p` | `3:4`  | `834x1112` |
| `720p` | `9:16` | `720x1280` |
| `720p` | `21:9` | `1470x630` |

## 能力与约束

| 项目   | Seedance 2.0                                     | Seedance 2.0 Fast                                |
| ---- | ------------------------------------------------ | ------------------------------------------------ |
| 主要定位 | 更高画质                                             | 更快生成与更低成本                                        |
| 时长   | `4-15` 秒，或 `-1` 自动                               | `4-12` 秒，或 `-1` 自动                               |
| 分辨率  | `480p` / `720p`                                  | `480p` / `720p`                                  |
| 图片角色 | `first_frame` / `last_frame` / `reference_image` | `first_frame` / `last_frame` / `reference_image` |
| 视频角色 | `reference_video`                                | `reference_video`                                |
| 音频角色 | `reference_audio`                                | `reference_audio`                                |
| 音频生成 | `metadata.generate_audio`                        | `metadata.generate_audio`                        |
| 工具   | `metadata.tools`                                 | `metadata.tools`                                 |
| 返回尾帧 | `metadata.return_last_frame`                     | `metadata.return_last_frame`                     |

<Info>
  按秒计费，实际价格可能随模型版本、分辨率和平台展示策略调整，请以 [模型价格页](https://toapis.com/models) 为准。
</Info>

## Response

<ResponseField name="id" type="string">
  任务唯一标识符，用于查询任务状态
</ResponseField>

<ResponseField name="object" type="string">
  对象类型，固定为 `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  使用的模型名称
</ResponseField>

<ResponseField name="status" type="string">
  任务状态

  * `queued` - 排队等待处理
  * `in_progress` - 处理中
  * `completed` - 成功完成
  * `failed` - 失败
</ResponseField>

<ResponseField name="progress" type="integer">
  任务进度百分比（0-100）
</ResponseField>

<ResponseField name="created_at" type="integer">
  任务创建时间戳（Unix 时间戳）
</ResponseField>

<ResponseField name="metadata" type="object">
  任务元数据
</ResponseField>

<Info>
  视频生成为异步任务，提交后会返回任务 ID。可使用 [获取视频任务状态](/cn/api-reference/tasks/video-status) 查询进度和结果。
</Info>

<RequestExample>
  ```bash cURL（多模态参考生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "全程使用视频1的第一视角构图，全程使用音频1作为背景音乐。第一人称视角果茶广告，首帧参考图片1，尾部定格为图片2，保留清脆摇晃声与轻快节奏。",
      "duration": 11,
      "aspect_ratio": "16:9",
      "image_with_roles": [
        {"url": "https://example.com/ref-image-1.jpg", "role": "reference_image"},
        {"url": "https://example.com/ref-image-2.jpg", "role": "reference_image"}
      ],
      "video_with_roles": [
        {"url": "https://example.com/ref-video-1.mp4", "role": "reference_video"}
      ],
      "audio_with_roles": [
        {"url": "https://example.com/ref-audio-1.mp3", "role": "reference_audio"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": true
      }
    }'
  ```

  ```bash cURL（首尾帧图生视频） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "镜头从窗边花束慢慢推到餐桌中央，画面色调温暖柔和。",
      "duration": 5,
      "aspect_ratio": "adaptive",
      "image_with_roles": [
        {"url": "https://example.com/first-frame.png", "role": "first_frame"},
        {"url": "https://example.com/last-frame.png", "role": "last_frame"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": false,
        "return_last_frame": true
      }
    }'
  ```

  ```bash cURL（文生视频，开启联网搜索） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "微距镜头拍摄一只玻璃蛙停在叶片上，镜头缓慢切到透明腹部与跳动心脏，纪录片风格。",
      "duration": 11,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "generate_audio": true,
        "tools": [{"type": "web_search"}]
      }
    }'
  ```

  ```bash cURL（Fast 版本快速预览） theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0-fast",
      "prompt": "一只柴犬在雨后的霓虹街头奔跑，地面倒映彩色灯光。",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "doubao-seedance-2-0",
          "prompt": "镜头从咖啡馆室内平稳推进到街景，保留参考人物说话节奏与环境氛围",
          "duration": -1,
          "aspect_ratio": "adaptive",
          "image_with_roles": [
              {"url": "https://example.com/ref.png", "role": "reference_image"}
          ],
          "metadata": {
              "resolution": "720p",
              "generate_audio": True,
              "tools": [{"type": "web_search"}],
          },
      },
  )

  print(response.json())
  ```
</RequestExample>
