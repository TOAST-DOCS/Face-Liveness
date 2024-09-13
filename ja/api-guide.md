## AI Service > Face Liveness > APIガイド

## API共通情報

### 入力画像ガイド

* 入力画像は、幅と高さの両方が80px以上でなければなりません。
    * 顔のサイズが最小60*60px以上でなければ顔認識ができません。
    * 画像のサイズが大きくなるほど、最小の顔サイズも大きくすることで、より正確に認識できます。
    * 画像で顔が占める割合が大きいほど、より正確に認識できます。
* 入力画像で顔の左右の角度(Yaw)と上下角度(Pitch)は全て45度以下にする必要があります。
* 画像最大サイズ:最大3MB(3,000,000Byte)
* サポート画像形式：PNG、JPEG
* 画像URLにポートを直接指定する場合、80, 443, 10000～12000ポートのみ使用可能です。

## API目次

### 顔スプーフィング検出API

#### リクエスト

* {appKey}と{secretKey}は、コンソール右上の**URL & Appkey** ボタンをクリックして確認が可能です。

[URI]

| メソッド | URI                                                                        |
|------|----------------------------------------------------------------------------|
| POST | https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing |

[リクエストヘッダ]

| 名前          | 値         | 説明            |
|---------------|-------------|-----------------|
| Authorization | {secretKey} | コンソールで発行されたセキュリティキー |

[Path Variable]

| 名前 | 説明 |
| --- | --- |
| appKey | 統合アプリキーまたはサービスアプリキー |

#### Content-Type: application/json

| 名前 | タイプ | 必須かどうか | デフォルト値 | 有効範囲 | 例 | 説明 |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | 顔検出に使用する画像 |
| image.url | string | △ |  |  | "https://..." | 画像のURL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64でエンコードされた画像バイト |
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | スプーフィング検出感度 |

* image.url, image.bytesのうち必ず1つだけが必要です。

#### Content-Type: multipart/form-data

| 名前 | タイプ | 必須かどうか | デフォルト値 | 有効範囲 | 例 | 説明 |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  | | image.png | 顔検出に使用する画像ファイル | 
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | スプーフィング検出感度 |

<details>
<summary>リクエスト例</summary>

``` shell
curl -X POST 'https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing' \
-H 'Authorization: {secretKey}' \
-H 'Content-Type: application/json;charset=UTF-8' \
-d '{
    "image": {
        "url":"https://..."
    }
}'
```

``` shell
curl -X POST 'https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing' \
-H 'Authorization: {secretKey}' \
-F imageFile=@image.png
```

</details>

#### レスポンス

[レスポンス本文ヘッダ]

| 名前          | タイプ    | 説明                             |
|---------------|---------|----------------------------------|
| header.isSuccessful  | Boolean | 分析APIの成否                   |
| header.resultCode    | Integer | 結果コード                          |
| header.resultMessage | String  | 結果メッセージ(成功時はSuccess、失敗時はエラー内容) |

[レスポンス本文データ]

| 名前 | タイプ | 必須 | 例 | 説明 |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | 検出した顔の数 |
| data.faceDetails[].bbox | object | O | - | 画像内で検出した顔の境界ボックス(bounding box)情報 |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | 画像内で検出した顔boxのx0座標 |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | 画像内で検出した顔boxのy0座標 |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | 画像内で検出した顔boxのx1座標 |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | 画像内で検出した顔boxのy1座標 |
| data.faceDetails[].landmarks | array | O | - | 顔の特徴 |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | 有効な値リスト:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | 顔の特徴のy座標 |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | 顔の特徴のx座標 |
| data.faceDetails[].spoofing | boolean |  | false | 顔スプーフィング有無 |
| data.faceDetails[].confidence | float | O | 99.9123 | 顔認識の信頼度 |

<details>
<summary>レスポンス本文の例</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "faceDetailCount": 1,
        "faceDetails": [
            {
                "bbox": {
                    "x0": 0.08727272727272728,
                    "y0": 0.29333333333333333,
                    "x1": 0.24363636363636362,
                    "y1": 0.6213333333333333
                },
                "landmarks": [
                    {
                        "type": "leftEye",
                        "x": 0.1290909090909091,
                        "y": 0.4266666666666667
                    },
                    {
                        "type": "rightEye",
                        "x": 0.2018181818181818,
                        "y": 0.432
                    },
                    {
                        "type": "nose",
                        "x": 0.16545454545454547,
                        "y": 0.49333333333333335
                    },
                    {
                        "type": "leftLip",
                        "x": 0.13818181818181818,
                        "y": 0.5546666666666666
                    },
                    {
                        "type": "rightLip",
                        "x": 0.19272727272727272,
                        "y": 0.56
                    }
                ],
                "spoofing": true,
                "confidence": 0.997854
            }
        ]
    }
}
```
</details>

#### Error Codes

| resultCode | resultMessage | 説明 |
| ---------- | ------------- | --- |
| -40000 | InvalidParam | パラメータにエラーがある |
| -41005 | UnauthorizedAppKeyOrSecretKey | 承認されていないAppkeyまたはSecretKey |
| -45020 | ImageTooLargeException | 画像サイズ超過 |
| -45030 | InvalidImageParameterException | 画像パラメータが無効です。主にBase64エンコーディングが間違っている場合に発生します。 |
| -45040 | InvalidImageFormatException | サポートしない画像形式 |
| -45050 | InvalidImageURLException | 無効な画像URL |
| -45060 | ImageTimeoutError | 画像ダウンロードタイムアウト |
| -50000 | InternalServerError | サーバーエラー |
