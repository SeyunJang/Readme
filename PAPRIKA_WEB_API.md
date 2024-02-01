# RESTful API lists (client-web server)

NOTE : Each API title is **temporarily** ‘function name’ declared in files.

NOTE : Body data for POST APIs are not clear enough and could possibly be different to ‘actual’ API specification.

---

## api.js


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
        
- fileUpload
    
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
    
    ```tsx
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
        
        - example : [https://test.send-anywhere.com/web/devices?device_ids=9523236732446,&_=1706166146513](https://test.send-anywhere.com/web/devices?device_ids=9523236732446,&_=1706166146513)
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
        
        - example : [https://file-15-165-3-114.send-anywhere.com/api/webfile/391388?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=keyinfo&_=1706166468699](https://file-15-165-3-114.send-anywhere.com/api/webfile/391388?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=keyinfo&_=1706166468699)
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
        
        - example : [https://file-15-165-3-114.send-anywhere.com/api/webfile/687MS4GK?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=list&start_pos=0&end_pos=10&_=1706167822897](https://file-15-165-3-114.send-anywhere.com/api/webfile/687MS4GK?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=list&start_pos=0&end_pos=10&_=1706167822897)
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
        
        - example : [https://file-15-165-3-114.send-anywhere.com/api/webfile/427450?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=status&_=1706169319390](https://file-15-165-3-114.send-anywhere.com/api/webfile/427450?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=status&_=1706169319390)
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
        
        - example : [https://file-15-165-3-114.send-anywhere.com/api/webfile/213827?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=cancel&_=1706169672050](https://file-15-165-3-114.send-anywhere.com/api/webfile/213827?device_key=b620cdd7b33aed21e387840af978be3fb9f13ec9e0b235f9fc2514ca49c035cf&mode=cancel&_=1706169672050)
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
                ...
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

## social.js (not used)

- getToday
    - Method : GET
    - URL : ${ WP_ENDPOINT }/sa/v1/today
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
    
    - params
        
        ```tsx
        {
            before: null || number, //last.date_gmt
            count: null || number,
            _: null || string //new Date().getTime()
        }
        ```
        
- getOnePost
    - Method : GET
    - URL : ${ WP_ENDPOINT }/sa/v1/today/post/${ id }
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
    - params
        
        ```tsx
        {
        	_: number //new Date().getTime()
        }
        ```
        
- exploreToday
    - Method : GET
    - URL : ${WP_ENDPOINT}/sa/v1/today/search/${type}
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
        - type : “posts” || “tags” || “users” || “files”
        
- queryToday
    - Method : GET
    - URL : ${ WP_ENDPOINT }/sa/v1/today/${ type }
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
        - type : “posts” || “tags” || “users” || “files”
    - params
        
        ```tsx
        	//unclear
        	{
              ...params,
              count: number,
              _: number //new Date().getTime()
          }
        ```
        
- getFeaturedLinks
    - Method : GET
    - URL : ${WP_ENDPOINT}/sa/v1/today/featured
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
    - params
        
        ```tsx
        {
        	_: number //new Date().getTime()
        }
        ```
        
- getTopLinks
    - Method : GET
    - URL : ${WP_ENDPOINT}/sa/v1/today/popular
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
    - params
        
        ```tsx
        {
        	_: number //new Date().getTime()
        }
        ```
        
- deletePost
    - Method : POST
    - URL : /web/social/post/delete/${ id }
        - id : post id
    - data
        
        ```tsx
        null
        ```
        
- reportPost
    - Method : POST
    - URL : /web/social/post/notify/${ id }
        - id : post id
    - data
        
        ```tsx
        null
        ```
        
- like
    - Method : POST
    - URL : /web/social/post/like/${ id }
        - id : post id
    - data
        
        ```tsx
        null
        ```
        
- unlike
    - Method : POST
    - URL : /web/social/post/unlike/${ id }
        - id : post id
    - data
        
        ```tsx
        null
        ```
        
- getComments
    - Method : GET
    - URL : ${WP_ENDPOINT}/sa/v1/today/comments/${id}
        - WP_ENDPOINT :
        
        ```tsx
        const WP_ENDPOINT = window.serverData.feed_endpoint || (process.env.NODE_ENV === 'development' ? 'https://d32ez0ud2fzh10.cloudfront.net/api' : 'https://feed.send-anywhere.com/api');
        ```
        
        - id : post id
    - params
        
        ```tsx
        {
        	_: number //new Date().getTime()
        }
        ```
        
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
        - id : commend id
    - data
        
        ```tsx
        null
        ```
        

---

## Others

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
                ... //more articles
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

## BUGs found

- account without password → when try to upgrade plan → onClick ‘setPassword’ → it redirects to wrong URL (/user/password/reset) 
-> first of all, it has to be web/user/password/reset (see router.jsx, duplicate paths)
-> but basically it has to be (/user/password/change)

- after setting/changing password -> error occurs on navigating back to '/user/settings'
-> Settings component return errors if hash is not set (see Settings.jsx)
-> it has to be redirected to '/user/settings#account' (see Success.jsx)
(const [activeTab, setActiveTab] = useState(hash || '#profile');) <- might be causing errors
