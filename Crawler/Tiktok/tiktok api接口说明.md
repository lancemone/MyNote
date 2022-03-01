# Tiktok Api调用说明


## /passport/account/info/v2/
### 功能
获取用户信息


## /aweme/v1/compliance/settings/
Java Class: com.ss.android.ugc.aweme.compliance.api.model.ComplianceSetting


## /service/2/device_register/
### 功能
device_id和install_id等注册.实现方法com.ss.android.deviceregister.b.d$a.a
### api信息
Method: POST
Body内容： 
``` json
{
    "magic_tag": "ss_app_log", 
    "header": {
        "display_name": "TikTok", 
        "update_version_code": 180841, 
        "manifest_version_code": 180841, 
        "app_version_minor": "", 
        "aid": 1180, 
        "channel": "googleplay", 
        "package": "com.ss.android.ugc.trill", 
        "app_version": "18.8.41", 
        "version_code": 180841, 
        "sdk_version": "2.12.1-rc.17", 
        "sdk_target_version": 29, 
        "git_hash": "27690874", 
        "os": "Android", 
        "os_version": "10", 
        "os_api": 29, 
        "device_model": "Pixel 2", 
        "device_brand": "google", 
        "device_manufacturer": "Google", 
        "cpu_abi": "arm64-v8a", 
        "release_build": "ac6e3ab_20210325", 
        "density_dpi": 420, 
        "display_density": "mdpi", 
        "resolution": "1794x1080", 
        "language": "en", 
        "timezone": 2, 
        "access": "wifi", 
        "not_request_sender": 0, 
        "rom": "5790879", 
        "rom_version": "QP1A.190711.019", 
        "cdid": "d46a32b7-7f82-462a-a945-6688617814dd", 
        "sig_hash": "aea615ab910015038f73c47e45d21466", 
        "gaid_limited": 0, 
        "google_aid": "eb6eab92-f0b6-495f-88ee-99ad6e4316fc", 
        "openudid": "e9870f1aa168b054", 
        "clientudid": "de7c7286-9212-4965-aa54-cf3417caf08d", 
        "region": "US", 
        "tz_name": "Africa/Cairo", 
        "tz_offset": 7200, 
        "install_id": "7038888397943621382", 
        "device_id": "7034495795127993862", 
        "req_id": "33716af6-31ab-4c31-b45b-5e2ac8240e7d", 
        "custom": {
            "is_kids_mode": 0, 
            "filter_warn": 0, 
            "web_ua": "Dalvik/2.1.0 (Linux; U; Android 10; Pixel 2 Build/QP1A.190711.019)", 
            "user_period": 0, 
            "user_mode": -1
        }, 
        "apk_first_install_time": 1639737024348, 
        "is_system_app": 0, 
        "sdk_flavor": "global"
    }, 
    "_gen_time": 1640057652653
}
```

body内容处理:  
1. 生成JSONObject对象
2. JSONObject转字符串生成byte[]
3. 调用`NetUtil.sendEncryptLog(String str, byte[] bArr, Context context, boolean z, String[] strArr)`方法
4. 调用`NetUtil.compressAndEncryptData(Context context, byte[] bArr)`方法，对byte[]进行gzip压缩后调用`com.bytedance.frameworks.core.a.b.a(byte[] bArr, int i2)` 再调用`EncryptorUtil.a(byte[] bArr, int i2)`,再调用native方法ttEncrypt,获取加密后的byte[]
5. 调用`com.ss.android.ugc.aweme.statistic.AppLogNetworkClient.a`请求数据


## /aweme/v2/feed/
### 功能
获取视频流数据,返回结果类型为application/x-protobuf，需解析实体
### 参数
```
sp:-1
type:0
max_cursor:0
min_cursor:0
count:6
volume:0.0
pull_type:4
req_from:enter_auto
gaid:e983e2fc-5aa6-4b6c-adee-021eac61eb3d
ad_user_agent:Mozilla%2F5.0+%28Linux%3B+Android+10%3B+Pixel+2+Build%2FQP1A.190711.019%3B+wv%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Version%2F4.0+Chrome%2F96.0.4664.92+Mobile+Safari%2F537.36
filter_warn:0
ad_personality_mode:0
address_book_access:2
cached_item_num:0
last_ad_show_interval:-1
vpa_content_choice:1
sound_output_device:0
cmpl_enc:AgICAw0AFockF-RP_dA-7qeIMtk5-_4VlG0Kf4TZYMNjHg
user_avatar_shrink:96_96
manifest_version_code:180841
_rticket:1639649177325
current_region:US
app_language:en
app_type:normal
iid:7038888397943621382
channel:googleplay
device_type:Pixel+2
language:en
cpu_support64:true
host_abi:arm64-v8a
locale:en
resolution:1080*1794
update_version_code:180841
ac2:wifi5g
cdid:fb49afb2-b508-4158-b761-b676b52e3821
sys_region:US
os_api:29
uoo:1
timezone_name:Africa%2FCairo
dpi:420
residence:US
ac:wifi
device_id:7034495795127993862
os_version:10
timezone_offset:7200
version_code:180841
app_name:trill
ab_version:18.8.41
version_name:18.8.41
device_brand:google
op_region:US
ssmix:a
device_platform:android
build_number:18.8.41
region:US
aid:1180
ts:1639649177
```

参数说明：  
1. 