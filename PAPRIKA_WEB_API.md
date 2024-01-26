# API Documentation for PAPRIKA_WEB FE
# RESTful API lists (client-web server)

---

NOTE : *Each API title is **temporarily** ‘function name’ declared in ‘src/modules/api.js’*

NOTE : *Body data for POST APIs are not clear and possibly be different to ‘actual’ API specification.*

---

## api.js

---

- RefreshToken
    - Method : POST
    - URL : web/auth/refreshtoken/${provider}
        - provider : ‘login’ || ‘google’ || ‘facebook’ || ‘chrome’ || ‘rakuten’
    - data :
    
    ```json
    { refreshToken: string; }
    ```
    
- updateDevice
    - Method : POST
    - URL : web/device
    - data :
    
    ```json
    {
        os_type: string;
        manufacturer: string;
        model_number: string;
        app_version: string;
        os_version: string;
        device_language: any;
    }
    ```
    
- createKey
    - Method : POST
    - URL :  web/key
    - data :
    
    ```json
    /*
    interface FileObject {
    		name: string;
    		size: number;
    }
    */
    
    {
        file: FileObject[] || any,
        nolog: boolean,
    		g-recaptcha-response: null || any,
    		mode: string,
    		pass_recaptcha: null || boolean,
    		skip_thumbnail: null || string
    };
    
    //example
    {
        "file": [
            {
                "name": "2024_CES.pdf",
                "size": 325434
            }
        ],
        "mode": "direct",
        "nolog": true,
        "pass_recaptcha": true
    }
    ```
    
    - response (example)
        
        ```tsx
        {
            "key": "079707",
            "created_time": 1706148853,
            "expires_time": 1706149453,
            "is_unlimited_duration_key": false,
            "is_subscribed": 0,
            "link": "http://sendanywhe.re/079707",
            "weblink": "https://file-15-165-76-31.send-anywhere.com/api/webfile/079707?device_key=ff003541d4b60a14c268e7ed1e9d38f79ef43c829f2b98c66045a182b7d3028e",
            "use_storage": 0,
            "has_password": 0
        }
        ```
        
    
- startTransferSession
    - Method : POST
    - URL : ${ weblink }
        - weblink :
        
        ```tsx
        //res.weblink = 'weblink' from response data of 'createKey' API request
        weblink = res.weblink.replace('webfile', 'session_start');
        ```
        
        - example : "[https://file-15-165-76-31.send-anywhere.com/api/session_start/079707?device_key=ff003541d4b60a14c268e7ed1e9d38f79ef43c829f2b98c66045a182b7d3028e](https://file-15-165-76-31.send-anywhere.com/api/webfile/079707?device_key=ff003541d4b60a14c268e7ed1e9d38f79ef43c829f2b98c66045a182b7d3028e)"
    - headers: { ‘Content-Type’ : ‘text/plain’ }
    - data :
    
    ```tsx
    /*
    interface FileObject {
    		name: string;
    		size: number;
    }
    */
    
    {
        file: FileObject[];
    }
    
    //example
    {
    		"file": [
    				{
    		        "name": "2024_CES.pdf",
    		        "size": 325434
    		     }
    		]
    }
    ```
    
    - response example
        
        ```tsx
        {
            "file": [
                {
                    "url": "https://{SERVER_IP}/api/session/file/95c6789e5e447434f5a0517a17ce84a7083483e8",
                    "name": "2024 RSK 인턴십 과제.pdf",
                    "key": "95c6789e5e447434f5a0517a17ce84a7083483e8",
                    "size": 325434
                }
            ],
            "state": "transfer",
            "no_retry": true,
            "key": "079707",
            "peer_device_id": "2161929591651"
        }
        ```
        
    
- finishTransferSession
    - Method : GET
    - URL : ${ weblink }
        - weblink
        
        ```tsx
        //state.transfer.keyData.weblink = redux state, 'weblink' from response data of 'createKey' API request
        weblink = state.transfer.keyData.weblink.replace('webfile', 'session_finish');
        ```
        
    - response example
        
        ```tsx
        {
            "state": "complete",
            "name": "2024 RSK 인턴십 과제.pdf",
            "sent": 325434,
            "size": 325434
        }
        ```
        
- cancelTransferSession
    - Method : GET
    - URL : ${ weblink } + ‘&mode=cancel’
        - weblink : ‘weblink’ from state.transfer.keyData (redux state)
        
        ```tsx
        //state.transfer.keyData.weblink = redux state, 'weblink' from response data of 'createKey' API request
        weblink = state.transfer.keyData.weblink.replace('webfile', 'session');
        ```
        
    - response example
        
        ```tsx
        {
            "transfer": 0,
            "length": 6910423,
            "mode": "cancel",
            "peer_device_id": "2161929591651",
            "name": "20240115_CES분석_다올투자증권.pdf"
        }
        ```
        
- ***fileUpload (unclear) → to update later***
    
    //url,fd
    
    - Method : POST
    - URL : ${ url }
        - url :
        
        ```tsx
        file.url.replace('{SERVER_IP}', l.hostname + ':' + l.port) +
                l.search +
                '&offset=' +
                last.sent
        ```
        
        - example
            
            https://file-3-39-236-109.send-anywhere.com/api/session/file/b074d70e135646d3c996732f654aee605e431332?device_key=ff003541d4b60a14c268e7ed1e9d38f79ef43c829f2b98c66045a182b7d3028e&offset=0
            
    - data :
    
    ```json
    fd = new FormData 
    fd.append(`${file.key}:${last.sent}-${end}`, chunk, file.name)
    ```
    
- setDownloadLimit
    - Method : POST
    - URL : /web/key/limit/${ key }
        - key : key value for files
            - key data from state.myLink.links (redux state)
        - example
            - /web/key/limit/F5PKFYLB
    - data :
    
    ```json
    {
    		download_limit : number || null
    }
    ```
    
    - response example
        
        ```tsx
        {"key":"F5PKFYLB"}
        ```
        
    
- searchKey
    - Method : GET
    - URL : /web/key/inquiry/${ key }
        - key : (6-digit) string to receive files via transfer. generated by sender. input key value the receiver entered at widget component
    - response example
        
        ```tsx
        {
            "server": "https://file-3-39-255-126.send-anywhere.com/api/",
            "key": "663939",
            "link": "http://sendanywhe.re/663939",
            "device_id": "2161929591651",
            "created_time": 1706156914,
            "expires_time": 1706157514,
            "download_count": 0,
            "use_storage": 0,
            "is_unlimited_duration_key": false
        }
        ```
        
    
- queryKey
    - Method : POST
    - URL : /web/key/search/${ key }
        - key : (6-digit) string to receive files via transfer. generated by sender. input key value the receiver entered at widget component
    - data :
    
    ```json
    null
    ```
    
    - response example
        
        ```tsx
        {
            "key": "663939",
            "device_id": "2161929591651",
            "mode": "direct",
            "expires_time": 1706157514,
            "current_remain_traffic": 0,
            "served_download_traffic": 0,
            "weblink": "https://file-3-39-255-126.send-anywhere.com/api/webfile/663939?device_key=ff003541d4b60a14c268e7ed1e9d38f79ef43c829f2b98c66045a182b7d3028e",
            "file_size": 42139247,
            "file_count": 1
        }
        ```
        
    
- getMyLinks
    - Method : GET
    - URL : web/users/me/keys
    - response example
        
        ```tsx
        [
            {
                "key": "X9E6CS8B",
                "mode": "upload",
                "link": "https://test.send-anywhere.com/web/downloads/X9E6CS8B",
                "device_id": "8485327047957",
                "created_time": 1705649711,
                "expires_time": 1706254511,
                "respite_period_start": null,
                "file_number": 1,
                "file_size": 248087,
                "server": "https://file-15-165-3-114.send-anywhere.com/api/",
                "download_count": 0,
                "is_unlimited_duration_key": false,
                "served_download_traffic": 0,
                "is_subscribed": 1,
                "status": "normal",
                "summary": "0-02-08-335f75f44111d3a1b70b9473f10e96980b77b952933f0203a5847ac2cb4c0029_27a42e35951.jpg",
                "thumbnail_url": "https://d32ez0ud2fzh10.cloudfront.net/thumbnail/8485327047957/X9E6CS8B?ut=1705649711",
                "feed_image_url": "https://d32ez0ud2fzh10.cloudfront.net/feed/8485327047957/X9E6CS8B?ut=1705649711",
                "is_virus_key": false,
                "uploaded": false,
                "email_alarm_status": false,
                "is_expired": false
            },
            {
                "key": "687MS4GK",
                "mode": "upload",
                "link": "https://test.send-anywhere.com/web/downloads/687MS4GK",
                "device_id": "9523236732446",
                "created_time": 1705904423,
                "expires_time": 1706509223,
                "respite_period_start": null,
                "file_number": 1,
                "file_size": 248087,
                "server": "https://file-15-165-3-114.send-anywhere.com/api/",
                "download_count": 4,
                "is_unlimited_duration_key": false,
                "served_download_traffic": 0,
                "is_subscribed": 1,
                "status": "normal",
                "summary": "0-02-08-335f75f44111d3a1b70b9473f10e96980b77b952933f0203a5847ac2cb4c0029_27a42e35951 (1).jpg",
                "thumbnail_url": "https://d32ez0ud2fzh10.cloudfront.net/thumbnail/9523236732446/687MS4GK?ut=1705904423",
                "feed_image_url": "https://d32ez0ud2fzh10.cloudfront.net/feed/9523236732446/687MS4GK?ut=1705904423",
                "is_virus_key": false,
                "uploaded": true,
                "email_alarm_status": false,
                "is_expired": false
            }
        ]
        ```
        
- renewKey
    - Method : POST
    - URL : web/key/renew/${ key }
        - key : key value for files
            - key data from state.myLink.links (redux state)
        - example : web/key/renew/F5PKFYLB
    - data :
    
    ```json
    {
    	exp_time = number
    }
    
    //example
    {
    	exp_time = 1707231599
    }
    ```
    
    - response example
        
        ```tsx
        {
            "key": "F5PKFYLB",
            "updated_time": 1706164910,
            "expires_time": 1707231599,
            "storage_usage": 1147042,
            "storage_limit": 805306368000,
            "traffic_usage": 0,
            "traffic_limit": 536870912000,
            "total_traffic_usage": 0,
            "using_unlimited_storage": 0,
            "uploading_unlimited_storage": 0,
            "one_time_upload": 32212254720,
            "max_unlimited_storage": 5368709120,
            "plan_code": "standard-1",
            "queue_in_use_traffic": 0
        }
        ```
        
- deleteKey
    - Method : DELETE
    - URL : web/key/${ key }
        - key : key value for files
            - key data from state.myLink.links (redux state)
    - response example
        
        ```tsx
        {"key":"F5PKFYLB"}
        ```
        
- setKeyPassword
    - Method : POST
    - URL : /web/key/password/${ key }
        - key : key value for files
            - key data from state.myLink.links (redux state)
    - data :
    
    ```json
    {
    	password: string
    }
    ```
    
    - response example
        
        ```tsx
        {"key":"687MS4GK","has_password":1}
        ```
        
- setKeyEmailAlarm
    - Method : POST
    - URL : web/key/email/${ key }
        - key : key value for files
            - key data from state.myLink.links (redux state)
    - data :
    
    ```json
    {
    	email_alarm_status: boolean
    }
    ```
    
    - response example
        
        ```tsx
        {
            "key": "687MS4GK",
            "email_alarm_status": true
        }
        ```
        
- searchDeviceInfo
    - Method :
    - URL : web/devices?device_ids=${ devices }
        - devices :
        
        ```tsx
        let devices = '';
        
        for (var i = 0; i < deviceIds.length; i++) {
                devices += deviceIds[i] + ',';
            }
        ```
        
        - example : https://test.send-anywhere.com/web/devices?device_ids=9523236732446,&_=1706166146513
    - response example
        
        ```tsx
        [
            {
                "device_id": "9523236732446",
                "push_id": null,
                "device_name": "Mac Chrome Browser",
                "profile_name": "Hydra",
                "app_name": "Send Anywhere Chrome Extension",
                "os_type": "web",
                "model_number": "Chrome",
                "manufacturer": "Mac",
                "app_version": "2.0.0",
                "package_name": null,
                "api_key_id": 13,
                "push_key": null,
                "use_push": false,
                "uploaded_time": null,
                "has_pushid": false
            }
        ]
        ```
        
    
- keyInfo
    - Method : GET
    - URL : ${ weblink }
        - weblink :
        
        ```tsx
        // data = response data from searchKey API request
        /* 
        //example
        data = {
            "server": "https://file-3-39-255-126.send-anywhere.com/api/",
            "key": "663939",
            "link": "http://sendanywhe.re/663939",
            "device_id": "2161929591651",
            "created_time": 1706156914,
            "expires_time": 1706157514,
            "download_count": 0,
            "use_storage": 0,
            "is_unlimited_duration_key": false
        }
        */
        
        weblink = `${data.server}webfile/${data.key}?device_key=${getCookie('device_key')}`
        ```
        
        - example : https://file-15-165-3-114.send-anywhere.com/api/webfile/391388?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=keyinfo&_=1706166468699
    - data :
    
    ```json
    {
    	mode: 'keyinfo'
    }
    ```
    
    - response example
        
        ```tsx
        {
            "num_files": 1,
            "total_size": 42139247
        }
        ```
        
- fileList
    - Method : GET
    - URL : ${ weblink }
        
        ```
        weblink = `${server}webfile/${key}?device_key=${getCookie('device_key')}`
        ```
        
        - example : https://file-15-165-3-114.send-anywhere.com/api/webfile/687MS4GK?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=list&start_pos=0&end_pos=10&_=1706167822897
    - params :
    
    ```json
    {
        mode: 'list',
        start_pos: start,
        end_pos: end
    }
    ```
    
    - response example
        
        ```tsx
        {
            "file": [
                {
                    "name": "0-02-08-335f75f44111d3a1b70b9473f10e96980b77b952933f0203a5847ac2cb4c0029_27a42e35951 (1).jpg",
                    "size": 248087,
                    "key": "b9ca325f83baedd25a5d0139330a0e2cdfce5131",
                    "virus": false,
                    "virus_info": "",
                    "downloadable": true
                }
            ],
            "scanned": true,
            "detected": false
        }
        ```
        
- resumeTransferSession (not used)
    - Method : GET
    - URL : ${ weblink }
    
    ```tsx
    //res.weblink = 'weblink' from response data of 'queryKey' API request
    weblink = res.weblink.replace('webfile', 'session');
    ```
    
    - params :
    
    ```json
    {
    	mode: 'resume'
    }
    ```
    
- getTransferStatus
    - Method : GET
    - URL : ${ weblink }
        
        ```tsx
        //res.weblink = 'weblink' from response data of 'queryKey' API request
        weblink = res.weblink
        ```
        
        - example : https://file-15-165-3-114.send-anywhere.com/api/webfile/427450?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=status&_=1706169319390
    - params :
    
    ```json
    {
    	mode: 'status'
    }
    ```
    
    - response example
        
        ```tsx
        {
            "transfer": 7422119,
            "length": 7422119,
            "name": "IMG_0057 (1).JPG",
            "peer_device_id": "9523236732446",
            "mode": "complete"
        }
        ```
        
- transferCancel
    - Method : GET
    - URL : ${ weblink }
        
        ```tsx
        //res.weblink = 'weblink' from response data of 'queryKey' API request
        weblink = res.weblink
        ```
        
        - example : https://file-15-165-3-114.send-anywhere.com/api/webfile/213827?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=cancel&_=1706169672050
    - params :
    
    ```json
    {
    	mode: 'cancel'
    }
    ```
    
    - response example
        
        ```tsx
        {
            "transfer": 5414662,
            "length": 7422119,
            "name": "IMG_0057 (1).JPG",
            "peer_device_id": "9523236732446",
            "mode": "cancel"
        }
        ```
        
- sendShareEmail
    - Method : POST
    - URL : web/email/widget
    - data :
    
    ```json
    {
    	"from": string,
        "to": [
            string
        ],
        "subject": string,
        "fileinfo": {
            "link": string,
            "exptime": number,
            "size": number,
            "number": number,
            "msg": string,
            "summary": string
        },
        "g-recaptcha-response": string,
    		"lng": string
    }
    
    //example
    {
        "from": "seyun1052@naver.com",
        "to": [
            "asdf@eoq.kr"
        ],
        "subject": "hello",
        "fileinfo": {
            "link": "http://sendanywhe.re/V1LE0K55",
            "exptime": 1706342889,
            "size": 325434,
            "number": 1,
            "msg": "hi :)",
            "summary": "2024 RSK 인턴십 과제 (1).pdf"
        },
        "g-recaptcha-response": "03AFcWeA6ZWc5TRjpA0_Qo5SOYTzecTAjGxrFmDkz1DfWzxSyKZos9T5UQfGJhjeW0dXVBF0WpAIGHRSnd6yqps_UN5Pfbe3CQq6hCoVjw4VYI9X2wXRlGmjR6-SPd_LWR9Fz6GvFGFd_BJn9jwpmmc0aFQ9xGoB17rlkUFXJ2o-TiTCW6lScJFeX7blSSWwvOePuxAWhTkDI_kEUj8i9Ai-3aUOJG2ayKbObX1EfrfwLU_Z6o7MZtP4BtXuVoxWWQmp6gobUnqJmN0ab1fKJKZOaOaN1vHntk7nCbq83IhX9ODMRZIFp0PznvodPJ_W_vjSwnkcEGfegPvWmMFaaagZHvRLV12a71PgNrAm9Y5wwhVcADss80fXJHxpGDReUUddxTdX9jANK0M3fdPHsvOZtz414muGtQ2aI-JoNsJH1Uo27RTW16XYZfLfdjYpFv4MibKj2b5PGUCrvBFVwjMEBxOLZR75I7pycSadjCTZ6zxHNAhWFYQVwvMcpx3zVk9Ebik5C9Ol30QE3d84T6NUtODCq7-NgceuErdUcWQaS8agMIgjwsJELa2ak7-yWXJYmblufwVnUR",
        "lng": "ko"
    }
    ```
    
    - response example
        
        ```tsx
        'complete'
        ```
        
- userLogin
    - Method : POST
    - URL : web/auth/${ provider }
        - provider : ‘login’ || ‘google’ || ‘facebook’ || ‘chrome’ || ‘rakuten’
    - data :
    
    ```json
    {
    		"token": string, //for social login only
    		"email": string, 
    		"password": string,//for default(email) login only
        "use_session": boolean,
        "pass_recaptcha": boolean
    }
    ```
    
    - response example
        
        ```tsx
        {}
        ```
        
    
- lockUser
    - Method : POST
    - URL : web/user/lock
    - data :
    
    ```json
    {
    	email : string
    }
    ```
    
    - response example
        
        ```tsx
        {
        	ok : true
        }
        ```
        
- unlockUser
    - Method : POST
    - URL : web/user/unlock
    - data :
    
    ```json
    {
        "pass1": string, //new password
        "pass2": string, //confirm password
        "ui": string, //cookie value of 'sa_ui'
        "ak": string //cookie value of 'sa_ak'
    }
    ```
    
- wakeUpUser
    - Method : POST
    - URL : web/user/wakeup
    - data :
    
    ```json
    null
    ```
    
- getUserSetting
    - Method : GET
    - URL : web/user/setting
    - response example
        
        ```tsx
        {
            "user_id": "kj10522002@gmail.com",
            "marketing_consent_timestamp": "1705976490",
            "marketing_consent": true,
            "have_password": true,
            "verified": true,
            "uid": "f8888f3bd08a30e12eeb1cca7b59235132088a78ee4949bc564c6f0bddb502cb"
        }
        ```
        
- putMarketingConsent
    - Method : PUT
    - URL : /web/user/setting
    - data :
    
    ```json
    {
        "marketing_consent": boolean
    }
    ```
    
    - response example
        
        ```tsx
        {
            "user_id": "kj10522002@gmail.com",
            "marketing_consent_timestamp": "1706235267",
            "marketing_consent": true
        }
        ```
        
    
- oauthConnect
    - Method : POST
    - URL : web/users/me/${ provider }
        - provider : ‘login’ || ‘google’ || ‘facebook’ || ‘chrome’ || ‘rakuten’
    - data :
    
    ```json
    {
    	token: string //requestToken
    	provider: string //ex. 'rakuten'
    	email: string 
    	rakuten_auth_type: string, //ex. 'setting'
    	jidState: boolean
    }
    ```
    
- checkCountry (not used)
    - Method : GET
    - URL : web/device/country
    - params :
    
    ```json
    //example
    {
    	os_type: 'web',
      manufacturer: BrowserDetect.OS,
      model_number: BrowserDetect.browser,
      app_version: process.env.APP_VERSION,
      os_version: BrowserDetect.version,
      device_language: window.navigator.userLanguage || window.navigator.language,
    }
    ```
    
- createAccount
    - Method : POST
    - URL : web/users
    - data :
    
    ```json
    {
    	'user_id': string,
      'password': string,
      'g-recaptcha-response': string
    }
    ```
    
- deleteAccount
    - Method : POST
    - URL : web/users/me/leave
    - data :
    
    ```json
    {
    	password : string
    }
    ```
    
- userLogout
    - Method : POST
    - URL : web/auth/logout
    - data :
    
    ```json
    null
    ```
    
- editPassword
    - Method : web/users/me/password
    - URL : PUT
    - data :
    
    ```json
    {
        "password": string, //new password
        "originalPassword": null || string, //only on update, not used if reset
        "update": null || boolean, //only on update, not used if reset
        "method": null || string //"POST", only on update, not used if reset
    }
    ```
    
- getMyDeviceList
    - Method : GET
    - URL : web/users/me/devices
    - response example
        
        ```tsx
        [
            {
                "device_id": "2161929591651",
                "profile_name": "SEYUNEDGE",
                "os_type": "web",
                "has_pushid": false,
                "device_name": "Mac Chrome Browser",
                "profile_url": null
            }
        ]
        ```
        
- sendApiKeyRequest
    - Method : POST
    - URL : web/v1/apikey/request
    - data :
    
    ```json
    {
        "fullname": string,
        "email": string,
        "company": string,
        "position": string,
        "desc": string
    }
    ```
    
    - response example
        
        ```tsx
        {
            "ok": true
        }
        ```
        
    
- getWebappVersion
    - Method : GET
    - URL : web/version
    - response example
        
        ```tsx
        "2.0.0"
        ```
        
    
- sendPasswordResetEmail
    - Method : POST
    - URL : web/user/password/reset
    - data :
    
    ```json
    {
        "email": string,
        "lng": string, //"ko", "en", "ja"
        "g-recaptcha-response": string
    }
    ```
    
- updatePassword
    - Method : POST
    - URL : web/user/password/update
    - data :
    
    ```json
    {
    	ak: string, //from cookie "sa_ak"
    	pass1: string, //new password
    	pass2: string, //password confirm
    	ui: string //from cookie "sa_ui"
    }
    ```
    
- sendToDevicePush
    - Method : POST
    - URL : web/key/push/${ key }
        - key : key value for files
            - state.transfer.keyData.key (redux state)
        - URL example : web/key/push/9523236732446:8HS4RE8K
    - data :
    
    ```json
    {
    	device_id: string
    }
    ```
    
- searchNearby
    - Method : GET
    - URL : web/device/nearby
    - response example
        
        ```tsx
        {
            "device": [
                {
                    "device_id": "9866479200657",
                    "profile_name": "kj-ipad2",
                    "device_name": "Apple iPad8,6",
                    "os_type": "ios",
                    "has_pushid": true,
                    "profile_url": "https://d32ez0ud2fzh10.cloudfront.net/profile/9866479200657"
                },
                {
                    "device_id": "9737582330249",
                    "profile_name": "KR-PGDCLK7DDF",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "8765066575964",
                    "profile_name": "ibank-osx-new",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "8189281656963",
                    "profile_name": "Min's mac",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "6292369069153",
                    "profile_name": "kr-m2k1wq6410",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "6189554797465",
                    "profile_name": "Windows Test",
                    "device_name": "Windows Desktop",
                    "os_type": "windows",
                    "has_pushid": true
                },
                {
                    "device_id": "5834790861152",
                    "profile_name": "SAY_951108_★",
                    "device_name": "Windows Desktop",
                    "os_type": "windows",
                    "has_pushid": true
                },
                {
                    "device_id": "5158484614248",
                    "profile_name": "iPhone 15 Pro",
                    "device_name": "Apple null",
                    "os_type": "ios",
                    "has_pushid": true
                },
                {
                    "device_id": "3276279895866",
                    "profile_name": "KR-MC7WGGH2DP",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "2972673139150",
                    "profile_name": "Min's mac",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "2917346329551",
                    "profile_name": "KR-M7W9GCHHQ2",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                },
                {
                    "device_id": "2025960009134",
                    "profile_name": "sdk_gphone64_arm64",
                    "device_name": "Google sdk_gphone64_arm64",
                    "os_type": "android",
                    "has_pushid": true
                },
                {
                    "device_id": "1685363503444",
                    "profile_name": "sdk_gphone64_arm64",
                    "device_name": "Google sdk_gphone64_arm64",
                    "os_type": "android",
                    "has_pushid": true
                },
                {
                    "device_id": "1348434866451",
                    "profile_name": "KR-M9MGVPC4HM",
                    "device_name": "macOS Desktop",
                    "os_type": "osx",
                    "has_pushid": true
                }
            ]
        }
        ```
        
- getProfileImageUrl
    - Method : GET
    - URL : web/device/profile/upload
    - response example
        
        ```tsx
        {
            "upload_url": "https://s3.us-west-2.amazonaws.com/sa.img.test/profile/9523236732446?AWSAccessKeyId=ASIA5LL4QEMVKIB5H4GV&Content-Type=image%2Fjpeg&Expires=1706248443&Signature=RCp121DgIGoXEAuoeUcuECjMTME%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEOX%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaDmFwLW5vcnRoZWFzdC0yIkcwRQIhALhfJiJ3ozqGxTWvm7J69U9m8hL6vxumVUqTUch4LqFxAiAwGKBYIPsryeSSzyWn2JGJr%2FJWiiip8hXeUwUpbkJPzCrUBQif%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDkxNzc3MzQ5MzAzNCIMsr9EoJ6kmhPOgcRvKqgFs7p47n2rbEQz0hOp3ksqdCEqjraVeSi3yHc9m%2BujxhWAEQFPU94eYCmxO5r5LapwcUTNXkV6CeOiGTOPCBLorLoSHzUZ7n8J2bfeOCDyhAzGPjPrHr%2FTU2%2B%2BZdXA298vc%2F2PJq41cCsUv1hN8kUiLLFGYpFjkRY0UocpTtaygYnp4EPQFy5%2FXhz7buvCLZM4OKqAiZ1On0CdGydh9vG9qk%2F2Erke9MI1myzYyFZqGellSIYG95AcmcYLkJG6MaLWtgm1HClU3Ox7X%2BlGrh6Gn8ZUm67isPh4jblTxVcRusGRIMyFtwFyyrnb%2F%2BAvs%2Bi4RWCuURowMu%2FQQ%2F5BSPSJ9OzP2s9up09RRkPMtMAjSp8gAgD%2BmHT0lo38R%2FNfXkKiLQya%2FxaVRJ4yL9ftS06LIJoWusyNiMQhNpw5WTnPhFvkINBFBqmLHyHGdQQXP1PGsTCLPUTCKyiZ5%2B%2BCKq4PS45c%2BvNA6Vi9neMBroG%2BslqPZAxsWWQI4%2BIHlykSi4oTudjK%2BE40DSOIb4T8N933vpHjOem1GKZAcMZfi7F9qJJcdYI3oeCh2cJBORFW%2F82n7Vkb6fZviSby1FZhtTJNy7eVMSuH%2FnYYHNBR5i2W5GVlqc8yJ7vomW4dwBRmctvdi%2BnOmpCkZwRUNAKfr5Nqo8qI3TrrqghsjtH0SbXA5C9QW4KWvxwIIHBN6Kv3tDitRrD50R%2BxuQSE56mlxzOYkSxe%2F7D7H7DsrgyDP6jiVFikN4jmmHyHIj9kBCByZA8C9BntYghEHJd5qFxBJ7%2FU%2FQUA0vZkMjCppAACwV%2By6jAf3glXD98zx6CwFxrm5yl1pMXpSt7c9tWy4xtK6N83DpzXmn8DFUC5IW2LwgEs%2FA6VU2aWO%2BdjaVHFtWTVwLeVt2PR%2Fep6DJ8woIPNrQY6sQE6hGGWBltZpaFJBw%2BN%2BcLoxYTQy532reVfaKaNmbN6ez5yBJPA2FWLT2K8gQGPdt1EiBT3joOIWy3cImuRhuTj9OE1H7toX6B8dswLDvbxxW%2FiTB4ZzRdCpR0oMSX%2BqfGB5oBLB4GyN6QRDN5AEa0rPXJCQxeck%2BCPuK9NH9a%2BUfJlZlVg%2Fn80ka%2FM7xIhZrZPzCYjPBFo%2Fn7X1f%2Fd4XpH%2FX6AoMYUlnOmqxbnBvINKCk%3D"
        }
        ```
        
- deleteProfileImage
    - Method : DELETE
    - URL : web/device/profile/upload
    - response example
        
        ```tsx
        {
        	ok: true
        }
        ```
        
    
- getMylinkUsage
    - Method : GET
    - URL : web/users/me/usage
    - response example
        
        ```tsx
        {
            "storage_usage": 0,
            "storage_limit": 10737418240,
            "traffic_usage": 0,
            "traffic_limit": 10737418240,
            "total_traffic_usage": 0,
            "using_unlimited_storage": 0,
            "uploading_unlimited_storage": 0,
            "one_time_upload": 10737418240,
            "max_unlimited_storage": 0,
            "plan_code": "normal-code",
            "queue_in_use_traffic": 0
        }
        ```
        
- resendVerifyEmail
    - Method : POST
    - URL : web/user/email/verify
    - data :
    
    ```json
    null
    ```
    
- getBGImagePermission
    - Method : GET
    - URL : web/user/image/upload/${ place }
        - place : ‘widget’ || ‘landing’ || ‘not_set’
    - params :
    
    ```json
    {
    	format: string //image.type.replace('image/', '')
    }
    
    //example
    {
    	format: 'png'
    }
    ```
    
    - response example
        
        ```tsx
        {
            "upload_url": "https://s3.us-west-2.amazonaws.com/sa.img.test/widget/84989f690519f58674e174137d003166bd4471c1?AWSAccessKeyId=ASIA5LL4QEMVGKSYVNQW&Content-Type=image%2Fpng&Expires=1706251247&Signature=y8JVGwKRukl2IaO7Fk1rne9p1GI%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEOb%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaDmFwLW5vcnRoZWFzdC0yIkgwRgIhAM9QnjeGe9iqJgTdwpzPOEi4m9tjsSxIN0EzHGhBJYRUAiEAyq4uJakzYzbhPUF34DuXrgj4oDhAeTWoec19iD%2FLLCYq1AUIoP%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAAGgw5MTc3NzM0OTMwMzQiDL4Tk8vSdYd0Y1v6diqoBZr5RzCqTi5aRn5Tq8kZIzxzaQjSomjqbV%2FzmTCJuMPh2D%2FbyWsi1KFZDvWpx4Kqf4xVyGtyR1SW%2FHnQWHE6%2F2vSWb%2B3ta%2FI4afLEgYVa1ilbKJrgYc0%2FcJ1v94CwoPZoVzcaSKD4gVaYngUqrssJb8ybbjY0FUFCcKqNxNzkJlZWPNH6r%2FIORKQnotcpBpM0rgL93MSMzhXVnbZaqq5zAdYyWcV1P%2BnjEL25LMPp%2BBcVW%2FX%2FDQGEzi2zPuAQbsLG%2FDQyjsaEDceihxrjtlLwIj%2Bst8C89wVUWAKwNUf1HNZnwUuT8hOZo7aXOANHlKbOEITPQMo5804%2BYaLiqcg%2FP2hG0v2lwJWQzUXVEf%2FGvTzbJuoTC%2BECwP6w8b8juaqF2ySweZaUmI8hYAvsPD8RuqUgH76P2FFWKCgwhmnyfi%2BLHXU0j2MXvC%2FzENqk8%2BhH4aYW90bDzoeOp9UDqQuvey3McMu15Gv%2BMoz%2F5R98R7oUjw3xAlqhSaNkDaKEnFf%2B7T0y64%2BHFLr3DQSUjlQ5Jv8PViM9zWv4J90jHS%2BezjkxqJHLTnMs2eD1TwfGveO9HlgkVK7Ryemmh5NRiuq0lb78ZcoS0EDc4Ljn%2FRJ8y%2FdoXLqR7WhOOMGYfwxyXOmB5lGq4SgRLt5gzGf9hu%2FG0G4rdGGIvF%2BTV1H7F9R9%2BKYW13DucMKCEPxbAcRm%2BWSXqwV2thDPrJY9LL0Uw55nivIh9HkmvJfS9jyqto87DMvHUIIsvHqH%2FJH7gBCrIVL7uaXLxHczMgUtqa%2Fex1nWgZ%2B31D9HLFaSepOdp%2F5yNJeNfAX0pshS3VLOdhElmRW2wgIhkeOKI3td1hHRcxCWbDjP8NQKdAC%2Bcn%2BdfEDhNPFOKRj8HqXbF1IS1%2BBFqxx0aCo23tgy0nqMLifza0GOrAB95JNRGc%2FJ1EFtTmm2L7thgH%2Fn7AqvZV0DnHBRHLLkOn%2F4qB8TmUsFSIbN1qMFDlRJvu51I5m%2BMmHDgiRGAgzO1MXzOPsNQIg9mjvJZLo%2Bvz4dprsvGiZ5sRHXblq1zMT9PR7T5PGvy384IsjwjbvO8W4X22aUwSGdImNOtQ0Of5bMrxDMVDGyQFTAShLaYvwZVB0mRsxm%2BIAq%2FvAXju9QD4HcCnHYRAl4d7hQzin%2FG4%3D"
        }
        ```
        
    
- queryBGImage
    - Method : GET
    - URL : web/user/image/${ place }
        - place : ‘widget’ || ‘landing’ || ‘not_set’
    - response example
        
        ```tsx
        //null if BGImage is not set yet
        //OR
        //if there's BGImage to show then response is as below
        {
            "image_url": "https://d32ez0ud2fzh10.cloudfront.net/widget/84989f690519f58674e174137d003166bd4471c1?ut=1706250347047"
        }
        ```
        
- deleteBGImage
    - Method : DELETE
    - URL : web/user/image/${ place }
        - place : ‘widget’ || ‘landing’ || ‘not_set’
    - response example
        
        ```tsx
        {
        	"ok": true
        }
        ```
        
    
- createSocialPost (to be tested)
    - Method : POST
    - URL : web/social/post/create
    - data :
    
    ```json
    {
    	key: string,
    	message: string,
    	tags: string
    }
    ```
    
    - response example
        
        ```tsx
        //to be updated
        ```
        
    
- setLandingImageLink
    - Method : POST
    - URL : web/user/image/link
    - data :
    
    ```json
    {
    	link: string
    }
    ```
    
    - response example
        
        ```tsx
        {
        	ok: true
        }
        ```
        
    

---

## payments.js (not tested)

---

- createCheckoutSession
    - Method : POST
    - URL : /web/subscriptions/sessions
    - data
        
        ```tsx
        {
        	plan: string,
        	stripeEmail: string,
        	mode: string, //'setup' || 'subscription' || 'payment'
        	coupon : null || string //null if mode == 'setup'
        }
        ```
        
- subscribe (not used)
    - Method : POST
    - URL : /web/subscriptions
    - data
        
        ```tsx
        //example
        {
            plan: "eaddon-standard-12m",
            // coupon: null,
            receipt_id: "6360ce26d01c7e00214dcec0",
            email: "seyun@eoq.kr",
            provider: 'bootpay',
        }
        ```
        
- updateSubscriptions
    - Method : POST
    - URL : /web/subscriptions/me
    - data
        
        ```tsx
        {
            stripeEmail: string,
        		stripeToken: string,
            plan: string,
            coupon: string
        }
        ```
        
- unsubscribe
    - Method : DELETE
    - URL : /web/subscriptions/me
- resume
    - Method : POST
    - URL : /web/subscriptions/me/resume
    - data
        
        ```tsx
        null
        ```
        
- getSubscriptions
    - Method : GET
    - URL : /web/subscriptions/me
- getPaymentHistory
    - Method : POST
    - URL : /web/subscriptions/me/invoices
- getUpcomingPayments
    - Method : POST
    - URL : /web/subscriptions/me/invoices/upcoming
    - data
        
        ```tsx
        {
        	plan : null || string
        }
        ```
        
- checkCoupon
    - Method : POST
    - URL : /web/subscriptions/me/coupons
    - data
        
        ```tsx
        {
        	coupon: string,
        	plan: null || string
        }
        ```
        

---

## social.js (to be updated, to be tested) (not used_unclear)

- getToday (to be updated, below apis as well)
    - Method : GET
    - URL :
    - params
        
        ```tsx
        {
            before: null || last.date_gmt
            count: null || params.count || count,
            _: null || string //new Date().getTime()
        }
        ```
        
- getOnePost
- exploreToday
- queryToday
- getFeaturedLinks
- getTopLinks
- deletePost
    - Method : POST
    - URL : /web/social/post/delete/${ id }
        - id :
    - data
        
        ```tsx
        null
        ```
        
- reportPost
    - Method : POST
    - URL : /web/social/post/notify/${ id }
        - id :
    - data
        
        ```tsx
        null
        ```
        
- like
    - Method : POST
    - URL : /web/social/post/like/${ id }
        - id :
    - data
        
        ```tsx
        null
        ```
        
- unlike
    - Method : POST
    - URL : /web/social/post/unlike/${ id }
        - id :
    - data
        
        ```tsx
        null
        ```
        
- getComments
    - Method : GET
    - URL :
        - id :
- createComment
    - Method : POST
    - URL : /web/social/comment/create
    - data
        
        ```tsx
        {
        	pid: string,
        	message: string
        }
        ```
        
- deleteComment
    - Method : POST
    - URL : /web/social/comment/delete/${ id }
        - id :
    - data
        
        ```tsx
        null
        ```
        

---

## Others

---

- startFacebookSignIn (pages/OutlookSocialSignIn.jsx)
    - Method : GET
    - URL : `https://graph.facebook.com/me?fields=email&access_token=${accessToken}`
        - accessToken : window.location.href.split('#access_token=')[1].split("&")[0];
- getZendeskArticle (modules/notice.js)
    - Method : GET
    - URL : https://send-anywhere.zendesk.com/api/v2/help_center/${*locale*}/categories/360000312354/articles.json?sort_by=updated_at
        - locale : ‘ko’ || ‘en-us’ || ‘ja’
    - response example
        
        ```tsx
        {
            "count": 21,
            "next_page": null,
            "page": 1,
            "page_count": 1,
            "per_page": 30,
            "previous_page": null,
            "articles": [
                {
                    "id": 26679420183065,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/26679420183065.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/26679420183065",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-12-21T07:37:07Z",
                    "updated_at": "2023-12-21T08:59:05Z",
                    "name": "Send Anywhere プライバシーポリシー 改訂 案内 (2023.12.21)",
                    "title": "Send Anywhere プライバシーポリシー 改訂 案内 (2023.12.21)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-12-21T08:59:04Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610394,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>いつもSend Anywhereをご利用いただき、ありがとうございます。 </p>\n<p>Send Anywhereチームです。プライバシーポリシーの改訂に関しご案内申し上げます。</p>\n<p>RAKUTEN SYMPHONY KOREA, INC.（以下、「当社」）は、Send Anywhereのサービス提供をより円滑にするため、プライバシーポリシーを改訂いたします。改訂後の内容については、下記および改訂後のプライバシーポリシー全文をご参照ください。</p>\n<p>プライバシーポリシー改訂の施行日までに別途異議申し立てがない場合、お客様は本改訂に同意したものとみなされます。尚、本改訂に同意されない場合は、退会の申し出をしていただくことが可能です。</p>\n<p> </p>\n<p><strong>[</strong><strong>主な改訂</strong><strong>内</strong><strong>容</strong><strong>]</strong></p>\n<ul>\n<li>\n<p>一部デザインの調整</p>\n</li>\n<li>\n<p>各種法令遵守のための内容の追加</p>\n</li>\n<li>\n<p>収集する個人情報項目の整理</p>\n</li>\n<li>\n<p>第三者提供に関する内容の整理および追加</p>\n</li>\n</ul>\n<p>詳細は、改訂後の <a href=\"https://send-anywhere.com/ja/terms#privacy\"><span style=\"color: blue;\">プライバシ</span><span style=\"color: blue;\">ー</span><span style=\"color: blue;\">ポリシ</span><span style=\"color: blue;\">ー</span></a>をご参照ください。</p>\n<p> </p>\n<p><strong>[</strong><strong>適用時期</strong><strong>]</strong></p>\n<p>改訂後のプライバシーポリシーは、<strong>2024</strong><strong>年</strong><strong>1</strong><strong>月</strong><strong>20</strong><strong>日</strong>より適用開始いたします。</p>\n<p> </p>\n<p>本件に関するお問い合わせは、<a href=\"https://support.send-anywhere.com/hc/ja/requests/new\">カスタマーサポート</a>または <a href=\"mailto:support@send-anywhere.com\">support@send-anywhere.com</a> までご連絡ください。</p>\n<p>今後とも、Send Anywhereをどうぞよろしくお願い申し上げます。</p>\n<p> </p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 24782255400345,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/24782255400345.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/24782255400345",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-11-03T07:13:29Z",
                    "updated_at": "2023-11-14T00:29:17Z",
                    "name": "[サーバーメンテナンス完了] Send Anywhere サーバー定期点検のご案内 (2023.11.03)",
                    "title": "[サーバーメンテナンス完了] Send Anywhere サーバー定期点検のご案内 (2023.11.03)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-11-14T00:28:56Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610394,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><strong>サーバーメンテナンス完了のお知らせ</strong></p>\n<p> </p>\n<p>サーバーメンテナンスが完了し、通常通りサービスを再開しましたことを、お知らせいたします。</p>\n<p>ご協力いただき誠にありがとうございました。</p>\n<p> </p>\n<p><strong>【メンテナンス完了時間】</strong></p>\n<p><span style=\"font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;\">2023年 11月14日</span> 午前8時(GMT+9)</p>\n<p> </p>\n<p>Send Anywhereは今後もより一層のサービス向上を目指して、誠心誠意を尽く発展して参ります。</p>\n<p>引き続きSend Anywhereのご愛顧のほど何卒よろしくお願いいたします。</p>\n<p>他にご不明な点や質問がございましたら、<a href=\"mailto:support@send-anywhere.com\" target=\"_blank\" rel=\"noreferrer noopener\">support@send-anywhere.com</a>または<a href=\"https://support.send-anywhere.com/hc/ja\" target=\"_blank\" rel=\"noreferrer noopener\">ヘルプセンター</a>にお問い合わせください。 ありがとうございます。 </p>\n<p> </p>\n<p>Send Anywhereチーム</p>\n<p> </p>\n<p>================================================================</p>\n<p>いつもSend Anywhereをご利用いただきありがとうございます。</p>\n<p> </p>\n<p>より高速で安定したサービスを提供するため、サーバーの定期点検を実施いたします。</p>\n<p>下記の日程で定期点検が行われる予定ですので、皆様のご理解とご協力をお願いいたします。</p>\n<p> </p>\n<p><strong>点検日程</strong></p>\n<ul>\n<li>\n<p><span style=\"font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;\">日時: 2023年 11月14日 (火)</span></p>\n</li>\n<li>\n<p><span style=\"font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;\">時間: 午前 7:00 ~ 午後 9:00 (GMT+9)</span></p>\n<ul>\n<li>\n<p>作業の進捗状況によって、点検時間が変更される場合がございます。</p>\n</li>\n</ul>\n</li>\n</ul>\n<p><strong>点検 目的</strong></p>\n<ul>\n<li>\n<p>サービス品質向上のためのサーバー定期点検</p>\n</li>\n</ul>\n<p> </p>\n<p>点検中は、やむを得ず全てのプラットフォームにてSend Anywhereへのアクセスが制限されるため、サービスのご利用が困難になる場合があります。</p>\n<p>これはサービスの安定性及び性能向上のために必要な点検となっておりますので、ご理解とご協力をお願いいたします。</p>\n<p>サービスのご利用に際しご迷惑をおかけし申し訳ございません。最善を尽くし迅速に点検を終えられるようにいたします。</p>\n<p> </p>\n<p>皆様のご協力に感謝し、より良いサービスを提供するため引き続き努力して参ります。</p>\n<p>ご不明な点がございましたら、<a href=\"https://support@send-anywhere.com\">support@send-anywhere.com</a> または <a href=\"https://support.send-anywhere.com/hc/ja/requests/new\">カスタマーサポート</a> までお問い合わせください。</p>\n<p> </p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 20370916521625,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/20370916521625.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/20370916521625",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-07-06T06:53:20Z",
                    "updated_at": "2023-07-12T00:49:06Z",
                    "name": "[サーバーメンテナンス完了] Send Anywhere 定期点検のご案内 (2023.07.06)",
                    "title": "[サーバーメンテナンス完了] Send Anywhere 定期点検のご案内 (2023.07.06)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-07-12T00:49:06Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<div class=\"p-rich_text_section\">\n<p><strong>サーバーメンテナンス完了のお知らせ</strong></p>\n<p> </p>\n<p>いつもSend Anywhereをご利用いただきありがとうございます。</p>\n<p>サーバーメンテナンスが完了し、通常通りサービスを再開しましたことを、お知らせいたします。</p>\n<p>ご協力いただき誠にありがとうございました。</p>\n<p> </p>\n<p><strong>【メンテナンス完了時間】</strong></p>\n<p>2023年7月12日 午前8時(GMT+9)</p>\n<p> </p>\n<p>Send Anywhereは今後もより一層のサービス向上を目指して、誠心誠意を尽く発展して参ります。</p>\n<p>引き続きSend Anywhereのご愛顧のほど何卒よろしくお願いいたします。</p>\n<p>他にご不明な点や質問がございましたら、<a href=\"mailto:support@send-anywhere.com\" target=\"_blank\" rel=\"noreferrer noopener\">support@send-anywhere.com</a>または<a href=\"https://support.send-anywhere.com/hc/ja\" target=\"_blank\" rel=\"noreferrer noopener\">ヘルプセンター</a>にお問い合わせください。 ありがとうございます。 </p>\n<p> </p>\n<p>Send Anywhereチーム</p>\n<p>================================================================</p>\n</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">いつもSend Anywhereをご利用いただき、誠にありがとうございます。</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">速くて安定的な転送サービスを提供するために、サービスの定期点検を下記の日程で行いますので、ご了承ください。</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">\n<strong data-stringify-type=\"bold\">[点検日程]</strong><br>2023年 7月 12日</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">\n<strong data-stringify-type=\"bold\">[点検時間]</strong><br>午前 7:00 ~ 午前 9:00 (GMT+9)</div>\n<ul class=\"p-rich_text_list p-rich_text_list__bullet\" data-stringify-type=\"unordered-list\" data-indent=\"0\" data-border=\"0\">\n<li data-stringify-indent=\"0\" data-stringify-border=\"0\">作業の進捗状況によって、点検時間が変更される場合もございます。</li>\n</ul>\n<div class=\"p-rich_text_section\">\n<strong data-stringify-type=\"bold\">[内容]</strong><br>サービスの質を向上させるための、サーバー定期点検</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">サーバーの点検中には、ファイルの転送が断続的に不安定になることがございます。 サービスのご利用にご不便をおかけし恐れいりますが、何卒ご理解いただけますと有り難く存じます。</div>\n<div class=\"p-rich_text_section\">今後ともより良いサービスを提供できるよう、最善を努めて参りますので、何卒よろしくお願いいたします。</div>\n<div class=\"p-rich_text_section\">他にご不明な点や質問がございましたら、<a class=\"c-link c-link--focus-visible\" href=\"mailto:support@send-anywhere.com\" target=\"_blank\" rel=\"noopener noreferrer\" data-stringify-link=\"mailto:support@send-anywhere.com\" data-sk=\"tooltip_parent\" aria-haspopup=\"menu\" aria-expanded=\"false\">support@send-anywhere.com</a>または<a class=\"c-link c-link--focus-visible\" href=\"https://support.send-anywhere.com/hc/ja/requests/new\" target=\"_blank\" rel=\"noopener noreferrer\" data-stringify-link=\"https://support.send-anywhere.com/hc/ja/requests/new\" data-sk=\"tooltip_parent\">ヘルプセンター</a>にお問い合わせください。</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">ありがとうございます。</div>\n<div class=\"p-rich_text_section\"> </div>\n<div class=\"p-rich_text_section\">Send Anywhereチーム</div>"
                },
                {
                    "id": 18129414729241,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/18129414729241.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/18129414729241",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-05-03T01:58:01Z",
                    "updated_at": "2023-05-03T09:33:58Z",
                    "name": "Send Anywhere 利用規約 新設 案内 (2023.05.03)",
                    "title": "Send Anywhere 利用規約 新設 案内 (2023.05.03)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-05-03T09:33:48Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>いつもSend Anywhereをご利用くださりありがとうございます。 <br>Send Anywhereチームです。</p>\n<p>Send Anywhereの有料プラン“Send Anywhere For Email”の日本での提供開始に伴い、日本語版の利用規約を新設致しました。<br>主な改定内容に関しては【主な改定内容】を、全文に関しては下記リンクからご確認下さい。</p>\n<p>改定の施行日までに別途異議申し立てがない場合は、本改定に同意したものとみなします。なお、利用規約の改定に同意いただけない場合は、Send Anywhereの「設定」ページより、アカウントの削除を行ってください。<br>お問い合わせは<a class=\"external-link\" href=\"https://support.send-anywhere.com/hc/ja?_ga\" rel=\"nofollow\">カスタマーセンター</a>もしくは、<a class=\"external-link\" href=\"mailto:support@send-anywhere.com\" rel=\"nofollow\">support@send-anywhere.com</a> までご連絡ください。</p>\n<p><br><strong>【利用規約改定のご案内】</strong></p>\n<ul>\n<li>通知日： 2023年5月3日</li>\n<li>施行日： 2023年5月10日</li>\n<li>改定理由： Send Anywhereの有料プラン開始に伴う、日本語版利用規約の新設</li>\n<li>全文：<a class=\"external-link\" href=\"https://send-anywhere.com/ja/terms\" rel=\"nofollow\">こちらからご確認下さい</a>\n</li>\n</ul>\n<p><strong>【主な改定内容】</strong></p>\n<ul>\n<li>お客様の本サービスの利用申し込みを拒否させて頂く場合について明記しました。（3. 利用申し込みの承諾）</li>\n<li>お客様の本サービスご利用にあたっての禁止行為を明記しました。（5.制限）</li>\n<li>プラン変更を行われた場合、支払い方法を変更された場合について明記しました。（9.サービス料）</li>\n<li>消費者契約法改正に伴い、当社の責任範囲を明確化しました。（2.利用申し込み・10.API・11.補償・12.担保責任の排除・13.利用停止・14. 利用停止・提供終了）</li>\n<li>準拠法を日本法とし、第一審の専属的合意管轄裁判所を東京地方裁判所に変更しました。（17.雑則）</li>\n</ul>\n<p> </p>\n<p>ありがとうございます。<br>Send Anywhere</p>"
                },
                {
                    "id": 16479573128473,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/16479573128473.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/16479573128473-Notice-of-Policy-Change-for-Free-Users-2023-03-13",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-03-13T05:26:30Z",
                    "updated_at": "2023-03-13T06:04:06Z",
                    "name": "Notice of Policy Change for Free Users (2023.03.13)",
                    "title": "Notice of Policy Change for Free Users (2023.03.13)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-03-13T06:00:34Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>Hello, this is Send Anywhere.</p>\n<p>We would like to express our sincere gratitude to all of our users who use Send Anywhere and inform you that our policy on link downloads for free users has changed.</p>\n<p> </p>\n<p><strong>[Changed Service] </strong></p>\n<p>Link Downloads<br><br></p>\n<p><strong>[Policy Change]</strong></p>\n<ul>\n<li>Previous Policy: 10GB download limit per link for free users</li>\n<li>Changed Policy: 10GB monthly download limit per free user account (limit resets on the 1st of each month)<br><br>※ This download limit is for users who create and share links, not for the recipients of the links.<br>※ If the data usage limit is reached, all downloads of links created under the account will be restricted.</li>\n</ul>\n<p><br><strong>[Effective Date] </strong></p>\n<p>March 7, 2023</p>\n<p> </p>\n<p>Sending by six-digit keys remains unlimited for file transfers, as before. If you want your recipients to download from the links, please subscribe to <a href=\"https://send-anywhere.com/pricing\" target=\"_blank\" rel=\"noopener noreferrer\">Send Anywhere Email add-on</a> to add download data. Lite members can use up to 200GB of monthly download data, and Standard members can use up to 500GB.</p>\n<p>Send Anywhere Email Add-on subscribers can enjoy premium features such as faster and more stable transfers via dedicated servers, creating links of up to 30GB, generating up to 750GB of links, unlimited link creation without expiration date or password settings, and ad-free usage on all platforms.</p>\n<p> </p>\n<p>We will continue to strive to provide better services in the future.</p>\n<p>Thank you.</p>\n<p> </p>\n<p>Send Anywhere Team</p>"
                },
                {
                    "id": 16244855390105,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/16244855390105.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/16244855390105-Notice-for-Send-Anywhere-Terms-of-Service-Revision-2023-03-07",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-03-06T12:20:45Z",
                    "updated_at": "2023-03-07T05:48:13Z",
                    "name": "Notice for Send Anywhere Terms of Service Revision (2023.03.07)",
                    "title": "Notice for Send Anywhere Terms of Service Revision (2023.03.07)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-03-07T05:47:56Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>Thank you for using Send Anywhere. </p>\n<p> </p>\n<p>We are always grateful to everyone who uses Send Anywhere, and we would like to inform you about the changes in our new Terms of Service.</p>\n<p>Please refer to the information below so that there is no inconvenience in using our service.</p>\n<p> </p>\n<p><strong>[Major changes]</strong></p>\n<p>The service name has been changed due to changes in paid services.</p>\n<p>For full details, please refer to <a href=\"https://send-anywhere.com/terms\" target=\"_self\">the revised Terms of Service</a>.</p>\n<ul>\n<li><strong>Send Anywhere Plus → Send Anywhere Email Add-on</strong></li>\n</ul>\n<p> </p>\n<p><strong>[Time of change]</strong></p>\n<p>The revised Terms of Service will be effective from <strong>March 7, 2023</strong>.</p>\n<p> </p>\n<p>If you have any questions regarding changes to the Terms of Service, please contact the <a class=\"external-link\" href=\"https://support.send-anywhere.com/hc/en-us\" target=\"_blank\" rel=\"noopener noreferrer\">Help Center</a> or <a class=\"external-link\" href=\"mailto:support@estmob.com\" rel=\"nofollow\">support@estmob.com</a>. </p>\n<p> </p>\n<p>Thank you.</p>\n<p>Send Anywhere Team</p>"
                },
                {
                    "id": 15830691733017,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/15830691733017.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/15830691733017-Notice-on-our-new-paid-plan-release-Send-Anywhere-Email-Add-on-2023-03-07",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-02-22T07:14:14Z",
                    "updated_at": "2023-03-07T05:46:50Z",
                    "name": "Notice on our new paid plan release ‘Send Anywhere Email Add-on’ (2023.03.07)",
                    "title": "Notice on our new paid plan release ‘Send Anywhere Email Add-on’ (2023.03.07)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-03-07T05:46:50Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>Hello,<br>We are thrilled to announce that<strong> ‘Send Anywhere Add-on’</strong> has been released!  </p>\n<p> </p>\n<p>It provides rich functions such as email attachments of large files, a private server for subscribers(faster and more secure), and link customization.</p>\n<p>Please note that free use of Outlook Add-in, Chrome Extension, and Whale Extension will be restricted and will be converted to a subscriber-only service after the release date.</p>\n<p> </p>\n<p><strong>[Plan Types]</strong></p>\n<ul>\n<li>Send Anywhere Email Add-on Lite</li>\n<li>Send Anywhere Email Add-on Standard</li>\n</ul>\n<p><strong>[Changes]</strong></p>\n<ul>\n<li>Free users now receive 10GB of data on a monthly basis, resetting on the 1st of each month.</li>\n<li>The upload limit is 10 GB as before.</li>\n</ul>\n<p><strong>[Benefits]</strong></p>\n<ul>\n<li>By building a private server dedicated to subscribers, you can experience faster and more stable file transfer life.</li>\n<li>Detailed settings for My Link, such as expiration date, a limit on the number of downloads, alarms, and password settings, are available.</li>\n<li>Easily attach large files from Outlook and Gmail.</li>\n<li>All ads will be removed.</li>\n<li>Lite users can set up personal wallpapers on the website’s main screen and the download page of the link.</li>\n<li>Standard users can set links on the wallpapers.</li>\n<li>Data(Bandwidth) offered on a monthly basis: Lite: 200GB, Standard: 500GB</li>\n<li>Detailed link setting functions and monthly data are available when using our service on the Web, Extensions, Android, iOS, and Desktop app.</li>\n</ul>\n<p><strong>[Available Platforms]</strong></p>\n<ul>\n<li>Web</li>\n<li>Mobile App(Android, iOS)</li>\n<li>Desktop App(Windows, Mac OS, Linux)</li>\n<li>\n<p class=\"p1\">Microsoft 365 Outlook A<span style=\"font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;\">dd-in</span></p>\n</li>\n<li>Web Browser Extension(Google Chrome, Naver Whale)</li>\n</ul>"
                },
                {
                    "id": 15635254755737,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/15635254755737.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/15635254755737",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-02-16T08:04:38Z",
                    "updated_at": "2023-02-16T08:13:52Z",
                    "name": "Send Anywhere 定期点検のご案内 (2023.02.16)",
                    "title": "Send Anywhere 定期点検のご案内 (2023.02.16)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-02-16T08:13:51Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>いつもお世話になっております、Send Anywhereでございます。</p>\n<p>Send Anywhereをご利用いただき、誠にありがとうございます。</p>\n<p>速くて安定的な転送サービスを提供するために、サービスの定期点検を下記の日程で行いますので、ご了承ください。</p>\n<p> </p>\n<p><strong>[点検日程]</strong></p>\n<div class=\"c-message_kit__gutter\">\n<div class=\"c-message_kit__gutter__right\" data-qa=\"message_content\">\n<div class=\"c-message_kit__blocks c-message_kit__blocks--rich_text\">\n<div class=\"c-message__message_blocks c-message__message_blocks--rich_text\" data-qa=\"message-text\">\n<div class=\"p-block_kit_renderer\" data-qa=\"block-kit-renderer\">\n<div class=\"p-block_kit_renderer__block_wrapper p-block_kit_renderer__block_wrapper--first\">\n<div class=\"p-rich_text_block\" dir=\"auto\">\n<div class=\"p-rich_text_section\">2023年 2月 20日</div>\n</div>\n</div>\n</div>\n</div>\n</div>\n</div>\n</div>\n<div class=\"c-message_actions__container c-message__actions\">\n<div class=\"c-message_actions__group\" aria-label=\"메시지 작업\" data-qa=\"message-actions\"> </div>\n</div>\n<p><strong>[点検時間]</strong></p>\n<p>午前 7:00 ~ 午前 9:00 (GMT+9)</p>\n<ul>\n<li>作業の進捗状況によって、点検時間が変更される場合もございます。</li>\n</ul>\n<p><strong>[内容]</strong></p>\n<p>サービスの質を向上させるための、サーバ定期点検</p>\n<p> </p>\n<p>サーバの点検中には、ファイルの転送が断続的に不安定になることがございます。 サービスの利用にご不便をおかけして申し訳ございません。</p>\n<p>今後とも より良いサービスを提供できるよう努めて参りますので、何卒よろしくお願いいたします。</p>\n<p>他にご不明な点や質問がございましたら、<a href=\"https://support.send-anywhere.com/hc/ko/articles/support@estmob.com\"> support@estmob.com</a> またはヘルプセンターにお問い合わせください。</p>\n<p>ありがとうございます。 </p>\n<p> </p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 15634926888217,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/15634926888217.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/15634926888217",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2023-02-16T07:55:08Z",
                    "updated_at": "2023-02-16T07:58:35Z",
                    "name": "プライバシーポリシー改定のご案内 (2023.02.16)",
                    "title": "プライバシーポリシー改定のご案内 (2023.02.16)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2023-02-16T07:58:35Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>いつもSend Anywhere（センドエニウェア）をご利用いただきありがとうございます。</p>\n<p>Send Anywhereチームです。</p>\n<p>Send Anywhereのプライバシーポリシーの一部を改定いたしますことをご案内申し上げます。</p>\n<p><br>この改訂は、より内容を明確にするものであり、個人情報の利用目的、収集する情報範囲、規定などに変更はございません。</p>\n<p> </p>\n<p><strong>&lt;改定項目&gt;</strong></p>\n<ul>\n<li data-stringify-indent=\"0\" data-stringify-border=\"0\"><strong>Sendyの商品名変更に伴い、プライバシーポリシーから名称を削除</strong></li>\n</ul>\n<p>全体の変更内容は、<strong>改定されたプライバシーポリシー</strong>をご参照ください。</p>\n<p> </p>\n<p><strong>&lt;改定日時&gt;</strong><br>改定されたプライバシーポリシーは、<strong data-stringify-type=\"bold\">2023年 2月 16日</strong>より適用を開始いたします。</p>\n<p> </p>\n<p>プライバシーポリシー改定に関してご不明点がございましたら、<a class=\"c-link c-link--focus-visible\" href=\"https://support.send-anywhere.com/hc/ja\" target=\"_blank\" rel=\"noopener noreferrer\">カスタマーセンター</a>もしくは<a class=\"c-link c-link--focus-visible\" target=\"_blank\" rel=\"noopener noreferrer\">support@estmob.com</a> までお問い合わせください。引き続きご愛顧のほど何卒よろしくお願いいたします。</p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 6571892101913,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/6571892101913.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/6571892101913",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2022-05-13T09:40:28Z",
                    "updated_at": "2022-05-16T07:06:15Z",
                    "name": "プライバシーポリシー改定のご案内 (2022.05.13)",
                    "title": "プライバシーポリシー改定のご案内 (2022.05.13)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2022-05-16T07:06:15Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p> </p>\n<p>いつもSend Anywhere（センドエニウェア）をご利用いただきありがとうございます。</p>\n<p>Send Anywhereチームです。</p>\n<p>Send Anywhereのプライバシーポリシーの一部を改定いたしますことをご案内申し上げます。</p>\n<p><br>この改訂は、より内容を明確にするものであり、個人情報の利用目的、収集する情報範囲、規定などに変更はございません。</p>\n<p> </p>\n<p><strong>&lt;改定項目&gt;</strong></p>\n<ul>\n<li><strong data-stringify-type=\"bold\"> 収集する本人確認事項の追加</strong></li>\n<li><strong data-stringify-type=\"bold\">個人情報の利用目的、法的根拠の追加</strong></li>\n</ul>\n<p>全体の変更内容は、<strong data-stringify-type=\"bold\">改定されたプライバシーポリシー</strong>をご参照ください。</p>\n<p> </p>\n<p><strong>&lt;改定日時&gt;</strong><br>改定されたプライバシーポリシーは、<strong data-stringify-type=\"bold\">2022年 5月 16日</strong>より適用を開始いたします。</p>\n<p> </p>\n<p>プライバシーポリシー改定に関してご不明点がございましたら、<a class=\"c-link c-link--focus-visible\" tabindex=\"-1\" href=\"https://support.send-anywhere.com/hc/ja\" target=\"_blank\" rel=\"noopener noreferrer\" data-stringify-link=\"https://support.send-anywhere.com/hc/ja\" data-sk=\"tooltip_parent\" data-remove-tab-index=\"true\">カスタマーセンター</a>もしくは<a class=\"c-link c-link--focus-visible\" tabindex=\"-1\" target=\"_blank\" rel=\"noopener noreferrer\" data-stringify-link=\"mailto:support@estmob.com\" data-sk=\"tooltip_parent\" aria-haspopup=\"menu\" aria-expanded=\"false\" data-remove-tab-index=\"true\">support@estmob.com</a> までお問い合わせください。引き続きご愛顧のほど何卒よろしくお願いいたします。</p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 5345964754585,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/5345964754585.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/5345964754585",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2022-04-01T04:20:10Z",
                    "updated_at": "2022-04-01T04:46:55Z",
                    "name": "センドエニウェア 社名変更のご案内 (2022.04.01)",
                    "title": "センドエニウェア 社名変更のご案内 (2022.04.01)",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2022-04-01T04:46:55Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p class=\"p1\">いつもSend Anywhere（センド・エニウェア）をご利用いただきありがとうございます。</p>\n<p class=\"p1\">Send Anywhereチームです。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">ご利用いただくすべての方々に常に感謝しています。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">この度、Send Anywhereの提供会社である「Estmob,Inc. 」は、2022年4月1日付で「<strong>Rakuten Symphony Korea, Inc.</strong>」に社名を変更いたします。</p>\n<p class=\"p1\">本変更により、以下の利用規約とプライバシーポリシー内に表示される社名が「<strong>Rakuten Symphony Korea, Inc.</strong>」に変更されます。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\"><strong>＜ 変更対象 ＞</strong></p>\n<p class=\"p1\">・ 利用規約</p>\n<p class=\"p1\">・ APIサービスの利用規約</p>\n<p class=\"p1\">・ プライバシーポリシー</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">上記の改正事項は<strong>2022年4月8日</strong>から施行されます。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">Send Anywhereのこれまでの運営方針等の変更はございません。</p>\n<p class=\"p1\">従来と同様にご利用いただけます。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">本メールに関してご不明な点がございましたら、<a href=\"https://support.send-anywhere.com/hc/ja\"><span class=\"s1\">カスタマーセンター</span></a>または<a href=\"mailto:support@estmob.com\"><span class=\"s1\">support@estmob.com</span></a>までお問い合わせください。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">引き続き、ご愛顧のほど何卒よろしくお願いいたします。</p>\n<p class=\"p2\"> </p>\n<p class=\"p1\">Send Anywhere<span class=\"s2\">チーム</span></p>"
                },
                {
                    "id": 4410883835289,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/4410883835289.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/4410883835289",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2021-12-01T08:39:17Z",
                    "updated_at": "2021-12-06T06:45:03Z",
                    "name": "プライバシーポリシー改定のお知らせ (2021.12.01)",
                    "title": "プライバシーポリシー改定のお知らせ (2021.12.01)",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2021-12-06T06:43:35Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもSend anywhere(センドエニウェア)をご利用いただきありがとうございます。</span></p>\n<p><span style=\"font-weight: 400;\">センドエニウェアチームです。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">利用者の皆様によりよいサービスを提供していくことを目的として、</span></p>\n<p><span style=\"font-weight: 400;\">このたびSend anywhereのプライバシーポリシーが、下記のとおり変更となりますのでご案内申し上げます。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">【改定箇所】</span></p>\n<p><span style=\"font-weight: 400;\">Send anywhereのプライバシーポリシー</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">【改定点】</span></p>\n<ol>\n<li style=\"font-weight: 400;\" aria-level=\"1\"><span style=\"font-weight: 400;\">長期間サービスを利用しなかったユーザーに対するポリシーを追加</span></li>\n</ol>\n<ul>\n<li style=\"list-style-type: none;\">\n<ul>\n<li style=\"font-weight: 400;\" aria-level=\"1\"><span style=\"font-weight: 400;\">Send anywhereは個人情報保護のため、1年以上アクセスしなかったユーザーに対して休眠アカウントへの転換及びデータ分離保管を適用します。</span></li>\n</ul>\n</li>\n</ul>\n<p> </p>\n<p><span style=\"font-weight: 400;\">【実施日】</span></p>\n<p><span style=\"font-weight: 400;\">2021年12月7日</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">プライバシーポリシーの改定に同意しない場合、Send anywhereウェブサイトの設定画面ページからアカウントを削除できます。</span></p>\n<p><span style=\"font-weight: 400;\">プライバシーポリシー改定の実施日前までに別途の異議申し立てやアカウント削除等の手続きがない場合は、プライバシーポリシーの改定に同意したものとみなされ、変更内容が適用されます。</span></p>\n<p><span style=\"font-weight: 400;\">プライバシーポリシー改定に関して、その他にご不明な点がございましたら、カスタマーセンターまたはsupport@estmob.comまでご連絡ください。 </span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">Send anywhereは、より良いサービスを提供するために今後も改善に努めてまいります。</span></p>\n<p><span style=\"font-weight: 400;\">今後もSend anywhereをよろしくお願いいたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">センドエニウェアチーム</span></p>"
                },
                {
                    "id": 900003741986,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900003741986.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900003741986",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2020-11-27T06:07:06Z",
                    "updated_at": "2021-04-27T06:17:16Z",
                    "name": "Send Anywhereのサブスクリプションサービス「Send Anywhere PLUS」終了のご案内  [2020.11.30]",
                    "title": "Send Anywhereのサブスクリプションサービス「Send Anywhere PLUS」終了のご案内  [2020.11.30]",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2021-04-27T06:15:42Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">こんにちは。Send Anywhereです。 </span></p>\n<p><span style=\"font-weight: 400;\">いつもSend Anywhereをご利用いただき誠にありがとうございます。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">2018年3月ローンチ以降、 PLUS専用サーバーでのより快適なファイル転送と最大50GBの大容量ファイル転送、有効期限のないマイリンクなどの機能で皆様からご愛顧いただきました</span><strong>Send Anywhere PLUSが、もって提供を終了</strong><span style=\"font-weight: 400;\">いたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">Send Anywhere PLUSは終了いたしますが、 </span></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">ファイル数と容量制限のない6桁数字キー転送</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">最大10GBのリンク作成</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">セキュリティキーを別途入力しなくても簡単に転送ができるデバイス転送 など、</span></li>\n</ul>\n<p><strong>Send Anywhereの無料機能は引き続きご利用いただけます</strong><span style=\"font-weight: 400;\">。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">これまでSend Anywhere PLUSをご利用いただき、重ね重ね御礼申し上げます。</span></p>\n<p> </p>\n<p><strong>[終了するサービス]</strong></p>\n<ul>\n<li style=\"font-weight: 400;\">\n<span style=\"font-weight: 400;\">Send Anywhere サブスクリプションサービス：</span><strong>Send Anywhere PLUS</strong>\n</li>\n</ul>\n<p><strong> [終了日程]</strong></p>\n<ul>\n<li style=\"font-weight: 400;\">\n<strong>2020年12月31日以降は自動決済が停止</strong><span style=\"font-weight: 400;\">し、各ユーザー様の最終決済日に応じて</span><strong>最大2021年1月30日</strong><span style=\"font-weight: 400;\">までSend Anywhere PLUSの利用可能です。</span>\n</li>\n</ul>\n<p><span style=\"font-weight: 400;\">（例）</span></p>\n<ol>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">決済日:12月1日 → PLUS終了日:12月31日 → 2021年1月1日無料ユーザー切替</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">決済日:12月10日 → PLUS終了日:1月9日 → 2021年1月10日無料ユーザー切替</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">決済日:12月31日 → PLUS終了日:1月30日 → 2021年1月31日無料ユーザー切替</span></li>\n</ol>\n<p><strong>[PLUS終了時の注意事項] </strong></p>\n<ul>\n<li style=\"font-weight: 400;\">\n<span style=\"font-weight: 400;\">サービス終了は、</span><strong>サブスクリプションで提供していたPLUS機能のみ</strong><span style=\"font-weight: 400;\">に適用され、</span><strong>無料版は以前と同様にご利用いただけます</strong><span style=\"font-weight: 400;\">。</span>\n</li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">決済日による最後の有料期間が終了すると、全PLUSユーザーは自動的に無料ユーザーに移行します。</span></li>\n<li style=\"font-weight: 400;\">\n<span style=\"font-weight: 400;\">有料版が完全に終了後は、それ以降 PLUS機能をご利用できません。</span><strong>有効期限のないリンクは各ユーザーのサービス終了日+90日で有効期限日が自動設定されます</strong><span style=\"font-weight: 400;\">。 </span><strong>期限切れになったリンク内のファイルはすべて削除され、再び復旧できません</strong><span style=\"font-weight: 400;\">。期限が切れる前に重要なファイルは必ずバックアップしてください。 </span>\n</li>\n</ul>\n<p> </p>\n<p>今後ともより良いサービスを提供できるよう努めて参りますので、何卒よろしくお願いいたします。</p>\n<p> </p>\n<p>ありがとうございます。</p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 900005219686,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900005219686.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900005219686",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2021-03-17T08:31:29Z",
                    "updated_at": "2021-03-17T08:52:24Z",
                    "name": "Internet Explorer サポート終了のお知らせ(2021.03.17)",
                    "title": "Internet Explorer サポート終了のお知らせ(2021.03.17)",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2021-03-17T08:44:23Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもSend Anywhereをご利用いただき誠にありがとうございます。</span></p>\n<p><span style=\"font-weight: 400;\">センドエニウェアです。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">円滑なサービス提供のためサービスのため、センドエニウェアはInternet Explorer（インターネットエクスプローラー）ブラウザのサポートを終了いたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">Internet ExplorerでSend Anywhereをご使用されていたお客様は、お手数おかけしますが、今後は他のブラウザをご使用いただきますようお願い申し上げます。</span></p>\n<p><span style=\"font-weight: 400;\">また、円滑なSend Anywhereの利用には、<span class=\"wysiwyg-underline\"><a href=\"https://www.google.co.kr/intl/ja/chrome/?brand=IBEF&amp;gclid=Cj0KCQjw0caCBhCIARIsAGAfuMw5mRYQJ1ct-H992KzR7kRr0OX603LIawCNXJMr_xuLnxSMy_5WrRgaAtqgEALw_wcB&amp;gclsrc=aw.ds\" target=\"_blank\" rel=\"noopener\">Chrome(クロム)ブラウザ</a></span>の使用を推奨いたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">Send Anywhereは、今後もより快適なサービスをお届けできるよう邁進して参ります。</span></p>\n<p><span style=\"font-weight: 400;\">ご不明な点がございましたら、support@estmob.comまたはカスタマーセンターまでお問い合わせください。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">引き続きご愛顧のほど何卒よろしくお願いいたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">センドエニウェアチーム</span></p>"
                },
                {
                    "id": 900005293823,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900005293823.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900005293823",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2021-02-02T00:44:03Z",
                    "updated_at": "2021-02-02T00:46:36Z",
                    "name": "Send Anywhere 定期点検のご案内（2021.02.02）",
                    "title": "Send Anywhere 定期点検のご案内（2021.02.02）",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2021-02-02T00:46:36Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>いつもお世話になっております、Send Anywhereでございます。</p>\n<p>Send Anywhereをご利用いただき、誠にありがとうございます。</p>\n<p>速くて安定的な転送サービスを提供するために、サービスの定期点検を下記の日程で行いますので、ご了承ください。</p>\n<p> </p>\n<p><strong>[点検日程]</strong></p>\n<p>2021年 2月 4日</p>\n<p><strong>[点検時間]</strong></p>\n<p>午前 7:00 ~ 午前 9:00 (GMT+9)</p>\n<ul>\n<li>作業の進捗状況によって、点検時間が変更される場合もございます。</li>\n</ul>\n<p><strong>[内容]</strong></p>\n<p>サービスの質を向上させるための、サーバ定期点検</p>\n<p> </p>\n<p>サーバの点検中には、ファイルの転送が断続的に不安定になることがございます。 サービスの利用にご不便をおかけして申し訳ございません。</p>\n<p>今後とも より良いサービスを提供できるよう努めて参りますので、何卒よろしくお願いいたします。</p>\n<p>他にご不明な点や質問がございましたら、<a href=\"https://support.send-anywhere.com/hc/ko/articles/support@estmob.com\"> support@estmob.com</a> またはヘルプセンターにお問い合わせください。</p>\n<p>ありがとうございます。 </p>\n<p> </p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 900002404043,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900002404043.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900002404043",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2020-09-02T00:24:54Z",
                    "updated_at": "2020-09-02T00:27:57Z",
                    "name": "iOS 9 バージョン以下のサポート終了案内(2020.09.02)",
                    "title": "iOS 9 バージョン以下のサポート終了案内(2020.09.02)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2020-09-02T00:26:50Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもお世話になっております、Send Anywhereです。</span></p>\n<p><span style=\"font-weight: 400;\">Send Anywhereをご利用いただき、誠にありがとうございます。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">iOS 9.3以下バージョンでSend Anywhereのサポートが終了される予定です。</span></p>\n<p><span style=\"font-weight: 400;\">より円滑で安定的なサービスを提供するためですので、ご了承いただければ幸いです。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">サポート終了についての詳しい内容は下記の情報をご確認ください。</span></p>\n<p> </p>\n<p><strong>[終了日時]</strong></p>\n<ul>\n<li><span style=\"font-weight: 400;\">2020年 9月 4日</span></li>\n</ul>\n<p> </p>\n<p><strong>[終了内容] </strong></p>\n<ul>\n<li><span style=\"font-weight: 400;\">iOS 9.3 以下バージョンは、20.7.17以降バージョンでアップデート中断</span></li>\n</ul>\n<p> </p>\n<p><span style=\"font-weight: 400;\">終了時点から iOS 9.3 以下バージョンはSend Anywhereの最新バージョンにアップデートできず、新しい機能を使うためには、ご利用中のデバイスを iOS 10 以上のバージョンにアップグレードしてください。 </span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">今後ともより良いサービスを提供できるよう努めて参りますので、何卒よろしくお願いいたします。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">ありがとうございます。</span></p>\n<p><span style=\"font-weight: 400;\">Send Anywhereチーム</span></p>"
                },
                {
                    "id": 900001552943,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900001552943.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900001552943",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2020-06-26T04:54:49Z",
                    "updated_at": "2020-06-26T06:19:21Z",
                    "name": "Send Anywhere 定期点検のご案内（2020.06.26）",
                    "title": "Send Anywhere 定期点検のご案内（2020.06.26）",
                    "source_locale": "en-us",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2020-06-26T06:18:49Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもお世話になっております、Send Anywhereでございます。</span></p>\n<p><span style=\"font-weight: 400;\">Send Anywhereをご利用いただき、誠にありがとうございます。</span></p>\n<p><span style=\"font-weight: 400;\">速くて安定的な転送サービスを提供するために、サービスの定期点検を下記の日程で行いますので、ご了承ください。</span></p>\n<p><span style=\"font-weight: 400;\"> </span></p>\n<p><strong>[点検日程]</strong></p>\n<p><span style=\"font-weight: 400;\">2020年 6月 29日</span></p>\n<p><strong>[点検時間]</strong></p>\n<p><span style=\"font-weight: 400;\">午前 5:00 ~ 午前 7:00 (GMT+9)</span></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">作業の進捗状況によって、点検時間が変更される場合もございます。</span></li>\n</ul>\n<p><strong>[内容]</strong></p>\n<p><span style=\"font-weight: 400;\">サービスの質を向上させるための、サーバ定期点検</span></p>\n<p><span style=\"font-weight: 400;\"> </span></p>\n<p><span style=\"font-weight: 400;\">サーバの点検中には、ファイルの転送が断続的に不安定になることがございます。 サービスの利用にご不便をおかけして申し訳ございません。</span></p>\n<p><span style=\"font-weight: 400;\">今後とも より良いサービスを提供できるよう努めて参りますので、何卒よろしくお願いいたします。</span></p>\n<p><span style=\"font-weight: 400;\">他にご不明な点や質問がございましたら、</span><a href=\"https://support.send-anywhere.com/hc/ko/articles/support@estmob.com\"> <span style=\"font-weight: 400;\">support@estmob.com</span></a><span style=\"font-weight: 400;\"> またはヘルプセンターにお問い合わせください。</span></p>\n<p><span style=\"font-weight: 400;\">ありがとうございます。 </span></p>\n<p><span style=\"font-weight: 400;\"> </span></p>\n<p><span style=\"font-weight: 400;\">Send Anywhereチーム</span></p>"
                },
                {
                    "id": 900001227066,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900001227066.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900001227066",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2020-06-09T07:00:27Z",
                    "updated_at": "2020-06-10T05:29:15Z",
                    "name": "SendAnywhere PLUSサービス終了のお知らせ及びさらにパワフルになったSendy PROのご紹介 (2020.06.10)",
                    "title": "SendAnywhere PLUSサービス終了のお知らせ及びさらにパワフルになったSendy PROのご紹介 (2020.06.10)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2020-06-10T05:27:34Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもお世話になっております、SendAnywhereです。 </span></p>\n<p><span style=\"font-weight: 400;\">SendAnywhereをご利用頂いただき、誠にありがとうございました。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">速くて安定的な転送と、豊かなMyリンクストレージで愛され続けたSendAnywhere PLUSが6月30日にサービスサポートを中断、2020年12月31日をもってサービス終了予定です。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">今までSendAnywhere PLUSをご利用頂いただき、誠にありがとうございました。</span></p>\n<p> </p>\n<p><strong>[終了サービス]</strong></p>\n<p><span style=\"font-weight: 400;\">SendAnywhere PLUS</span></p>\n<p> </p>\n<p><strong>[終了日程]</strong></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">2020年 6月 10日 PLUS新規登録中断</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">2020年 6月 30日 PLUSサポート中断</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">2020年 12月 31日 PLUSサービス終了（予定）</span></li>\n</ul>\n<p> </p>\n<p><strong>[終了内容] </strong></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">2020年 6月 30日以降、サービスが中断されてもサービスが完全に終了されるまでには、以前と同じように購入及びサービスをご利用いただけます。</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">サポート中断及び終了に該当するのはPLUSのみであり、SendAnywhere無料プランは今後も引き続きご利用いただけます。</span></li>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">SendAnywhere PLUSの利用期間が終了するとリンク内の全ファイル、そして有効期限のないマイリンクでも全て削除されます。削除されたリンクは復元できませんので、ご利用期間終了前に予めバックアップをしておくことをおすすめします</span></li>\n<li style=\"font-weight: 400;\">\n<span style=\"font-weight: 400;\">もうこれ以上PLUSをご利用したくない場合は、Webサイトにログイン後、</span><a href=\"https://send-anywhere.com/user/settings/#plan\"><span style=\"font-weight: 400;\">設定 &gt; 料金プラン</span></a><span style=\"font-weight: 400;\">ページからSendAnywhere PLUSの定期支払いを解約できます。定期支払いを解約後も、すでにお支払いの1か月間はPLUSプランは継続いたします。</span>\n</li>\n</ul>\n<p> </p>\n<p><span style=\"font-weight: 400;\">SendAnywhere PLUSは終了いたしますが、 </span></p>\n<p><span style=\"font-weight: 400;\">SendAnywhere PLUSをさらにアップグレードしたSendy PROでは、より簡単かつ素早くファイルを転送、保存、管理まで一か所で解決できます！</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">Transfer と Cloud、２つの機能が完璧に組み合わさったSendy PROは、 Send Anywhere PLUSならではのパワフルな転送とマイリンク機能はもちろん、メールとメッセンジャーとの簡単連携、さらには1TBのクラウドストレージを提供しています。従来よりも一段階上へ進化したファイル管理に特化したサービスです。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">さらにパワフルになったファイル転送と管理サービス、 Sendy PROを今すぐご利用ください！</span></p>\n<p><a href=\"https://sendycloud.com/account/change-plan?utm_source=sa_plus_support&amp;utm_medium=button&amp;utm_campaign=plus_to_pro\" target=\"_blank\" rel=\"noopener\"><strong>&gt;&gt;Sendy PRO を始める</strong></a></p>"
                },
                {
                    "id": 900000070183,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/900000070183.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/900000070183",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2020-01-06T07:07:39Z",
                    "updated_at": "2020-01-07T08:30:56Z",
                    "name": "メールサーバのメンテナンスによるメール転送停止のご案内 (2020.01.06）",
                    "title": "メールサーバのメンテナンスによるメール転送停止のご案内 (2020.01.06）",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2020-01-07T08:28:35Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p><span style=\"font-weight: 400;\">いつもお世話になっております。</span></p>\n<p><span style=\"font-weight: 400;\">Send Anywhereをご利用いただき、誠にありがとうございます。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">メールサーバのメンテナンスは下記の予定で行われるで、予めご了承ください。</span></p>\n<p> </p>\n<p><strong>[メンテナンス日程]</strong></p>\n<p><span style=\"font-weight: 400;\">2020年 1月 11日</span></p>\n<p><strong>[メンテナンス時間]</strong></p>\n<p><span style=\"font-weight: 400;\">午後 3時~午後 9時</span><span style=\"font-weight: 400;\">(GMT+9)</span></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">作業の状況によって時間帯が変更となる場合もありますので，御承知おき願います。</span></li>\n</ul>\n<p><strong>[内容]</strong></p>\n<p><span style=\"font-weight: 400;\">メールサーバメンテナンスによる「メールでリンク転送」および「メール認証」サービスの一時停止</span></p>\n<ul>\n<li style=\"font-weight: 400;\"><span style=\"font-weight: 400;\">パスワードの確認メールは正常的に送信されます。</span></li>\n</ul>\n<p> </p>\n<p><span style=\"font-weight: 400;\">サーバメンテナンス中にファイル転送サービスをご利用する場合には、お手数をおかけしますが<strong>デバイス間での転送、1:1ダイレクト転送、リンクを生成した後に直接渡すなどの方法</strong>で転送サービスをご利用ください。</span></p>\n<p><span style=\"font-weight: 400;\">サービス利用にご不便をおかけして、申し訳ございません。</span></p>\n<p><span style=\"font-weight: 400;\"> </span></p>\n<p><span style=\"font-weight: 400;\">他にお問い合わせがございましたら、support@estmob.comかヘルプセンターにご連絡ください。</span></p>\n<p> </p>\n<p><span style=\"font-weight: 400;\">今後ともより良いサービスを提供できるよう、努めて参りますので何卒よろしくお願いいたします。</span></p>\n<p><span style=\"font-weight: 400;\">SendAnywhereチーム</span></p>"
                },
                {
                    "id": 360004430153,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/360004430153.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/360004430153",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 0,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2018-05-17T08:54:45Z",
                    "updated_at": "2019-02-28T08:58:05Z",
                    "name": "「リンク共有」サービス一部変更のお知ら(2019.02.28)",
                    "title": "「リンク共有」サービス一部変更のお知ら(2019.02.28)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2019-02-28T04:55:49Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610394,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>こんにちは、Send Anywhereです。</p>\n<p>平素はSend Anywhereをご利用いただき、誠にありがとうございます。</p>\n<p>ファイルをリンクで共有する「リンク共有」サービスの一部を変更させて頂くこととなりましたので、ご案内申し上げます。</p>\n<p> </p>\n<p><strong>[ 変更対象サービス]</strong><br>• リンク共有</p>\n<p><strong>[変更内容]</strong><br>• 一部のユーザーによる不正使用を防止するためラフィックの制限を開始<br>• 快適なサーバー環境のため、トラフィックを多く発生させる特定の共有リンクに限って制限されることもあります。</p>\n<p><strong>[導入予定日]</strong></p>\n<p><strong>2019年 3月 4日</strong></p>\n<p> </p>\n<p>一部のリンクが大量のトラフィックを発生することにより、サービスの品質が低下する恐れがあります。</p>\n<p>転送速度や安定性を高めるためにトラフィックの制限を実施することになりました。</p>\n<p>6桁のキーではトラフィックとは関係なく、以前と同じように制限なく転送することができます。</p>\n<p>ご不便をお掛けしますが、何卒ご理解の程宜しくお願い申し上げます。</p>\n<p>今後ともSend Anywhereをご愛顧くださいますようお願いいたします。</p>\n<p>Send Anywhereチーム</p>"
                },
                {
                    "id": 360015248473,
                    "url": "https://send-anywhere.zendesk.com/api/v2/help_center/ja/articles/360015248473.json",
                    "html_url": "https://support.send-anywhere.com/hc/ja/articles/360015248473",
                    "author_id": 1261789778,
                    "comments_disabled": true,
                    "draft": false,
                    "promoted": false,
                    "position": 3,
                    "vote_sum": 0,
                    "vote_count": 0,
                    "section_id": 201021268,
                    "created_at": "2019-01-03T08:38:16Z",
                    "updated_at": "2019-02-28T03:23:41Z",
                    "name": "Send Anywhere音楽プレーヤー変更のお知らせ(2018.12.19)",
                    "title": "Send Anywhere音楽プレーヤー変更のお知らせ(2018.12.19)",
                    "source_locale": "ko",
                    "locale": "ja",
                    "outdated": false,
                    "outdated_locales": [],
                    "edited_at": "2019-01-03T08:51:30Z",
                    "user_segment_id": null,
                    "permission_group_id": 1610374,
                    "content_tag_ids": [],
                    "label_names": [],
                    "body": "<p>こんにちは、Send Anywhereです。<br>平素はSend Anywhereをご利用いただき、誠にありがとうございます。<br> <br>Send Anywhere音楽プレーヤーが新しくなりました！<br>新しい音楽プレーヤーは、<br>独自のプレイリストが作成でき、歌詞、A-Bリピート再生（ユーザーが定義したオーディオの一部、<br>またはAとBの間の区間再生）、ミニプレーヤーに対応しています！</p>\n<p> </p>\n<p>[変更対象サービス]</p>\n<p>Send Anywhere音楽プレーヤー</p>\n<p> </p>\n<p>[変更内容]</p>\n<ul>\n<li>独自のプレイリストの作成</li>\n<li>音楽ファイルの検索</li>\n<li>歌詞とA-Bリピート再生 (歌詞は、音楽ファイルに歌詞が含まれる場合のみ利用できます。)</li>\n<li>ミニプレーヤー</li>\n</ul>\n<p> </p>\n<p>[導入予定日]</p>\n<ul>\n<li>2018年12月19日</li>\n</ul>\n<p> </p>\n<p><br>不具合報告・ご意見はもっと見る＞お問い合わせからお気軽にご連絡ください。</p>\n<p>今後ともSend Anywhereをご愛顧くださいますようお願いいたします。</p>\n<p><br>Send Anywhereチーム</p>"
                }
            ],
            "sort_by": "updated_at",
            "sort_order": "desc"
        }
        ```
        
- checkImageExist (components/Wallpaper.jsx)
    - Method : GET
    - URL : ${ url }
        
        ```tsx
        //res = response data of 'queryBGImage' API request
        url = state.user.widgetBG || state.user.landingBG || res.image_url
        ```
        
    - response
        
        ```tsx
        image file data
        ```
        
- checkImageExist (pages/Settings/BGImages.jsx)
    - Method: GET
    - URL: ${ url }
        
        ```tsx
        let url = state.user.widgetBG || state.user.landingBG
        ```
        
    - response
        
        ```tsx
        image file data
        ```
        
- uploadThumbnail ( upload() at ‘/utils/thumbnail.js’ )
    - Method : PUT
    - URL : ${ url }
        - url : “upload_url” from ‘getProfileImageUrl’ API response
    - headers : {’Content-Type’: ${ contentType }}
        - contentType : ‘image/jpeg’ (or etc.)
    - data
        
        ```tsx
        blob data
        ```
        
- bootpayPayment (modules/bootpay.js) (not used)
    - NOT USED
- bootpaySubscription (modules/bootpay.js) (not used)
    - NOT USED

---

+axios,fetch,xhrRequest 검색 후 정리, client-paprika web server/-other endpoints , to classified

*BUG found

- account without password → when try to upgrade plan → onClick ‘setPassword’ → it redirects to wrong URL (/user/password/reset) → it has to be (/web/user/password/change)

Bootpay.js → currently not in use, to list up function names at least
