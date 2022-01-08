---
title: 【iOS】 AES128加解密
date: 2022-1-8
categories:
- iOS
- Objective-C
tags:
- 加密
- AES128加密
---

## 前言

在项目开发中，为了安全性，我们常常会给一些数据或接口加密，其中最常见的有`AES`、`MD5`、`RAS`。这里主要介绍一下`AES128`在`iOS`中的加解密应用。

`AES`（英文：`Advanced Encryption Standard`）即**高级加密算法**，是一种对称加密算法，是美国联邦政府采用的一种区块加密标准，目前在实际应用中极为广泛。`AES`的区块长度固定为`128`比特，密钥长度则可以是`128`、`192`或`256`比特。更多介绍：[AES](https://zh.wikipedia.org/zh-cn/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)。

## AES128加密

在以下讲解中默认开启了`kCCOptionECBMode`和`kCCOptionPKCS7Padding`模式。

```
+ (NSString *)encrypt:(NSString *)text key:(NSString *)key {
    
    char keyPtr[kCCKeySizeAES128 + 1];
    memset(keyPtr, 0, sizeof(keyPtr));
    [key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
    
    NSData *data = [text dataUsingEncoding:NSUTF8StringEncoding];
    const void *bytesOfData = data.bytes;
    NSUInteger lengthOfData = data.length;
    
    size_t bufferSize = lengthOfData + kCCBlockSizeAES128;
    unsigned char *buffer = malloc(bufferSize);
    size_t numBytesEncrypted = 0;
    
    CCCryptorStatus cryptStatus = CCCrypt(kCCEncrypt,
                                          kCCAlgorithmAES128,
                                          kCCOptionECBMode | kCCOptionPKCS7Padding,
                                          keyPtr,
                                          kCCBlockSizeAES128,
                                          NULL,
                                          bytesOfData,
                                          lengthOfData,
                                          buffer,
                                          bufferSize,
                                          &numBytesEncrypted);
    
    if (cryptStatus == kCCSuccess) {
        NSData *resultData = [NSData dataWithBytesNoCopy:buffer length:numBytesEncrypted];
        return [resultData base64EncodedStringWithOptions:NSDataBase64EncodingEndLineWithLineFeed];
    }
    
    free(buffer);
    
    return @"";
}
```

例如，我们使用`密钥key`为`"abcd"`来加密字符串`"hello world"`，结果为：`eI+DId43IRPm+Mz4HPHhjg==`。

如果要验证这个结果是否正确，可以使用在线加解密网站进行验证：[AES encryption / decryption](https://the-x.cn/en-us/cryptography/Aes.aspx) 。可以发现，代码中的加密结果和在线网站加密的结果是一样的。

但是，直接使用一个明文的`密钥key`（如上面的`abcd`）来加密似乎不太安全，因此一般来说，我们提供的密钥`key`都会使用 *十六进制编码* 过的`密钥key`。这种情况下，在加密之前，必须先对 *十六进制编码* 过的`密钥key` 进行解码，然后再进行加密。如下：

```
+ (NSString *)encrypt:(NSString *)text key:(NSString *)key {
    
    key = [self stringFromHexString:key];
    
    char keyPtr[kCCKeySizeAES128 + 1];
    memset(keyPtr, 0, sizeof(keyPtr));
    [key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
    
    NSData *data = [text dataUsingEncoding:NSUTF8StringEncoding];
    const void *bytesOfData = data.bytes;
    NSUInteger lengthOfData = data.length;
    
    size_t bufferSize = lengthOfData + kCCBlockSizeAES128;
    unsigned char *buffer = malloc(bufferSize);
    size_t numBytesEncrypted = 0;
    
    CCCryptorStatus cryptStatus = CCCrypt(kCCEncrypt,
                                          kCCAlgorithmAES128,
                                          kCCOptionECBMode | kCCOptionPKCS7Padding,
                                          keyPtr,
                                          kCCBlockSizeAES128,
                                          NULL,
                                          bytesOfData,
                                          lengthOfData,
                                          buffer,
                                          bufferSize,
                                          &numBytesEncrypted);
    
    if (cryptStatus == kCCSuccess) {
        NSData *resultData = [NSData dataWithBytesNoCopy:buffer length:numBytesEncrypted];
        return [resultData base64EncodedStringWithOptions:NSDataBase64EncodingEndLineWithLineFeed];
    }
    
    free(buffer);
    
    return @"";
}
```

解码*十六进制编码* 过的`密钥key`:

```
#pragma mark - Private methods

+ (NSString *)stringFromHexString:(NSString *)hexString {

    NSMutableString *mString = [[NSMutableString alloc] init];
    int i = 0;
    do {
        NSString *hexCharString = [hexString substringWithRange:NSMakeRange(i, 2)];
        int value = 0;
        sscanf([hexCharString cStringUsingEncoding:NSASCIIStringEncoding], "%x", &value);
        // If hex value is "00", it means that the ASCII value is null as shown in the standard ASCII characters table. See
        // https://www.ibm.com/docs/en/xl-fortran-aix/16.1.0?topic=appendix-ascii-ebcdic-character-sets. For example, a
        // hex string like 395ef0005f29586a5bc5f4a0d6c55591 contains hex value "00". In this case, the hexCharString is equal
        // to "00" and the value is 0. So we have to fix this code by appending a string containing a single null.
        // Note: don't use `[mString appendString:@"\0"]` directly that is not going to work as expected!
        if (value == 0) {
            [mString appendFormat:@"\0", (char)value];
        } else {
            [mString appendFormat:@"%c", (char)value];
        }
        i += 2;
    } while (i < hexString.length);

    return mString;
}
```

注意，在上面的`stringFromHexString`方法中，我们有一行代码是`[mString appendFormat:@"\0", (char)value];` ，如果要正确解码一个*十六进制编码* 过的`密钥key`，这行代码**必须添加**，这会在解码时对*十六进制编码* 过的`密钥key`（包含有`00`）进行补位，因为`00`在`ASCII`标准表中的值时`null`，会导致解码出来的`key`值丢失，长度变短，从而导致加解密的结果和上面的在线加解密网站的加解密的结果不一样！（很多网上的十六进制解码都是错的，并没有对`00`进行补位处理）。

同样，我们使用`密钥key`为`"a21be01f00eac3f7d184d86f40e19d6a"`（十六进制编码过的`key`，注意其中包含`00`位）来加密字符串`"hello world"`，结果为：`XFVC7cHutYRpmxPyankz3w==`。结果依然和在线加解密一样。

## AES128解密

```
+ (NSString *)decrypt:(NSString *)text key:(NSString *)key {
    
    key = [self stringFromHexString:key];
    
    char keyPtr[kCCKeySizeAES128 + 1];
    memset(keyPtr, 0, sizeof(keyPtr));
    [key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
    
    NSData *data = [[NSData alloc] initWithBase64EncodedString:text options:NSDataBase64DecodingIgnoreUnknownCharacters];
    const void *bytesOfData = data.bytes;
    NSUInteger lengthOfData = data.length;
     
    size_t bufferSize = lengthOfData + kCCBlockSizeAES128;
    unsigned char *buffer = malloc(bufferSize);
    
    size_t numBytesCrypted = 0;
    CCCryptorStatus cryptStatus = CCCrypt(kCCDecrypt,
                                          kCCAlgorithmAES128,
                                          kCCOptionECBMode | kCCOptionPKCS7Padding,
                                          keyPtr,
                                          kCCBlockSizeAES128,
                                          NULL,
                                          bytesOfData,
                                          lengthOfData,
                                          buffer,
                                          bufferSize,
                                          &numBytesCrypted);
    
    if (cryptStatus == kCCSuccess) {
        NSData *resultData = [NSData dataWithBytesNoCopy:buffer length:numBytesCrypted];
        return [[NSString alloc] initWithData:resultData encoding:NSUTF8StringEncoding];
    }
    
    free(buffer);
    
    return @"";
}
```

我们把上面的加密结果`XFVC7cHutYRpmxPyankz3w==`进行解密，结果依然是`"hello world"`。也可以用上面说的在线加解密试试解密结果是否一样。

## 附录

[Demo地址：01-Demo-AES128](https://github.com/lchenfox/BlogDemos)
