---
title: JAVA 生成二维码
date: 2020-11-27 23:08:47
summary: JAVA 生成二维码
tags:
    - 二维码
    - QRCODE
---

## JAVA 生成二维码

### 第一步：引入依赖

```
            <dependency>
                <groupId>com.google.zxing</groupId>
                <artifactId>javase</artifactId>
                <version>3.3.0</version>
            </dependency>
```

### 第二步：创建 QrCodeUtil 生成二维码

```
public class QrCodeUtil {

    public static BitMatrix encode(String content ) {
        //设置图片的文字编码以及内边框
        Map<EncodeHintType, Object> hints = new HashMap<EncodeHintType, Object>();
        //编码
        hints.put(EncodeHintType.CHARACTER_SET, "UTF-8");
        hints.put(EncodeHintType.ERROR_CORRECTION , ErrorCorrectionLevel.M);
        hints.put(EncodeHintType.MARGIN , 2 );
        BitMatrix bitMatrix;
        try {
            //参数分别为：编码内容、编码类型、图片宽度、图片高度，设置参数
            bitMatrix = new MultiFormatWriter().encode(content, BarcodeFormat.QR_CODE, 300, 300,hints);
        }catch(WriterException e) {
            e.printStackTrace();
            return null;
        }
        return bitMatrix;

    }
}
```

### 第三步： web api 方式输出二维码

```
@RestController
@RequestMapping(value = "qrcode")
public class QrCodeController {

    @GetMapping(value = "encode")
    public void encode(HttpServletResponse response){

        BitMatrix bitMatrix = QrCodeUtil.encode("wanghuan");
        try {
            MatrixToImageWriter.writeToStream(bitMatrix , "png" , response.getOutputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```
