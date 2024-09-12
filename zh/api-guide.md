## AI Service > Face Liveness > API Guide

## API Common Information

### Input Image Guide

* Face images must be at least 80x80 px in width and height.
    * The face size must be at least 60\*60 px to be eligible for facial recognition.
    * As the image gets bigger, the minimum face size must get bigger as well for better facial recognition precision.
    * The bigger the proportion of the face area in the image, the more precise the facial recognition.
* In the input image, both left/right angle(Yaw) and up/down angle(Pitch) of the face must be 45 degrees or less.
* Maximum size of image file: 3 MB (3,000,000 bytes)
* Supported image formats: PNG, JPEG
* If you specify a port directly in the image URL, only ports 80, 443, and 10000 to 12000 are available.

## API Contents

### Face Spoofing Detection API

#### Request

* The {appKey} and {secretKey} can be found by clicking the **URL & Appkey** button at the top right of the console.

[URI]

| Method  | URI                                                                        |
|------|----------------------------------------------------------------------------|
| POST | https://face-liveness.api.nhncloudservice.com/v1.0/appkeys/{appKey}/spoofing |

[Request Header]

| Name            | Value           | Description              |
|---------------|-------------|-----------------|
| Authorization | {secretKey} | Security key issued from the console |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integration app key or service app key |

#### Content-Type: application/json

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| image | object | O |  |  | - | Image used for face detection |
| image.url | string | △ |  |  | "https://..." | Image URL |
| image.bytes | blob | △ |  |  | "/0j3Ohdk==..." | Base64-encoded Image bytes |
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | Spoofing detection sensitivity |

* Must have only one of either image.url or image.bytes.

#### Content-Type: multipart/form-data

| Name | Type | Required | Default value | Valid range | Examples | Description |
| --- | --- | --- | --- | --- | --- | --- |
| imageFile | file | O |  | | image.png | Image files for face detection | 
| spoofingCondition | string |  | "balanced" |  "weak", "balanced", "strict" | "balanced" | Spoofing detection sensitivity |

<details>
<summary>Request example</summary>

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
-H 'Content-Type: multipart/form-data' \
-F imageFile=@image.png
```

</details>

#### Response

[Response Body Header]

| Name            | Type      | Description                               |
|---------------|---------|----------------------------------|
| header.isSuccessful  | Boolean | Analysis API success or not                     |
| header.resultCode    | Integer | Result code                            |
| header.resultMessage | String  | Result message (success on success, error content on failure) |

[Response body data]

| Name | Type | Required | Examples | Description |
| --- | --- | --- | --- | --- |
| data.faceDetailCount | int | O | 1 | Number of faces recognized |
| data.faceDetails[].bbox | object | O | - | Bounding box information of a face detected in the image |
| data.faceDetails[].bbox.x0 | float | O | 0.123 | x0 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.y0 | float | O | 0.123 | y0 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.x1 | float | O | 0.123 | x1 coordinates of the face box detected in the image |
| data.faceDetails[].bbox.y1 | float | O | 0.123 | y1 coordinates of the face box detected in the image |
| data.faceDetails[].landmarks | array | O | - | Facial characteristics |
| data.faceDetails[].landmarks[].type | string | O | "leftEye" | List of valid values:<br>"leftEye", "rightEye", "nose", "leftLip", "rightLip" |
| data.faceDetails[].landmarks[].y | float | O | 0.362 | y coordinate of the facial characteristic |
| data.faceDetails[].landmarks[].x | float | O | 0.362 | x coordinate of the facial characteristic |
| data.faceDetails[].spoofing | boolean |  | false | Face spoofing or not |
| data.faceDetails[].confidence | float | O | 99.9123 | Face recognition confidence |

<details>
<summary>Response body example</summary>

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

| resultCode | resultMessage | Description |
| ---------- | ------------- | --- |
| -40000 | InvalidParam | The parameter contains an error |
| -41005 | UnauthorizedAppKeyOrSecretKey | Unauthorized Appkey or SecretKey |
| -45020 | ImageTooLargeException | Image size exceeded |
| -45030 | InvalidImageParameterException | Invalid image parameter. Mainly due to incorrect Base64 encoding |
| -45040 | InvalidImageFormatException | Unsupported image formats |
| -45050 | InvalidImageURLException | Invalid image URL |
| -45060 | ImageTimeoutError | Image download timeout |
| -50000 | InternalServerError | Server error |