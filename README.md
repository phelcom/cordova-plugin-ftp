# cordova-plugin-ftp

## Description

This cordova plugin is created to use ftp (client) in web/js.

Support both **iOS** and **Android** platform now.

You can do the following things:

- List a directory
- Create a directory
- Delete a directory (must be empty)
- Delete a file
- Download a file (with percent info)
- Upload a file (with percent info)
- Cancel upload/download

## Installation

```sh
$ cordova plugin add cordova-plugin-ftp
$ cordova prepare
```

Dependency:

- For iOS, the plugin depends on *CFNetwork.framework*, which has been added to plugin.xml (and `cordova prepare` will add it to platform project), so you don't need to do anything.
- But for Android, it depends on *com.android.support:support-v4:23.2.0*, which should be added to your platform project by hand.

## Usage
### Ionic 2 & TypeScript
#### Requirements:
* @angular/core *(included by default on ionic2)*
* lodash *(included by default on ionic2)*
* rxjs/Observable *(included by default on ionic2)*
* rxjs/Subscriber *(included by default on ionic2)*

You can access this plugin by typescript class `Ftp`.
#### Instalation
##### Using ionic-cli v3
```
ionic cordova plugin add https://github.com/olaferlandsen/cordova-plugin-ftp.git
```
##### Using ionic-cli v2
```
ionic plugin add https://github.com/olaferlandsen/cordova-plugin-ftp.git
```
##### Using ionic-cli v1, phonegap-cli or cordova-cli
```
cordova plugin add https://github.com/olaferlandsen/cordova-plugin-ftp.git
```
#### Import
##### from `providers/`, `pages/` or `app/`
```typescript
import '../../plugins/cordova-plugin-ftp/types/ftp';
```

#### Example (example on pages/home.ts)
```typescript
import '../../plugins/cordova-plugin-ftp/types/ftp';
@Component({
    selector    : 'page-home',
    templateUrl : 'home.html'
})
export class HomePage
{
    /**
     *
     */
    constructor (public navCtrl: NavController, public ftp: Ftp)
    {
    }
    /**
     *
     */
    ionViewDidLoad() {
        this.ftp.isAvailible().then(() => {
            this.ftp.connect('127.0.0.1', 'root', 'password').then(() => {
                this.ftp.upload('file.zip', '/var/www/file.zip').then(() => {
                    this.ftp.download('file.zip', '/var/www/file.zip').then(() => {
                        this.ftp.rm('/var/www/file.zip').then(() => {
                            this.ftp.disconnect();
                        })
                    })
                })
            });
        }, () => {
            // ups... FTP plugin is not available...
        })
    }
}
```
#### API
* static *isAvailible* ():Promise<any>
* static *basename* (path:`string`, suffix?:`string`):`string`
* static *dirname* (path:`string`):`string`
* static *connect* (hostname:`string`, username:`string`, password:`string`):Promise<any>
* static *upload* (local:`string`, remote:`string`):Promise<any>
* static *uploadWithProgress* (local:`string`, remote:`string`):Observable<any>
* static *download* (local:`string`, remote:`string`):Promise<any>
* static *downloadWithProgress*  (local:`string`, remote:`string`):Observable<any>
* static *rm* (remote:`string`):Promise<any>
* static *rmdir* (remote:`string`):Promise<any>
* static *ls* (remote:`string`):Promise<any>
* static *mkdir* (remote:`string`):Promise<any>``
* static *cancel* ():Promise<any>
* static *disconnect* ():Promise<any>


### Cordova & Phonegap
You can access this plugin by js object `window.cordova.plugin.ftp`.

#### Example
```javascript
window.cordova.plugin.ftp.connect('hostname.com', 'username', 'password', function () {
    window.cordova.plugin.ftp.upload('file.zip', '/var/www/file.zip', function (progress) {
        if (progress === 1) {
            window.cordova.plugin.ftp.download('file.zip', '/var/www/file.zip', function() {
                window.cordova.plugin.ftp.rm('/var/www/file.zip', function() {
                    // :D
                })
            })
        }
    })
})
```
- Refer to [demo.js](./demo.js) for usage demo.
- Refer to [ftp.js](./www/ftp.js) for all js APIs.

## Notice
Example:

```js
document.addEventListener("deviceready", onDeviceReady, false);

function onDeviceReady() {
    // First of all, connect to ftp server address without protocol prefix. e.g. "192.168.1.1:21", "ftp.xfally.github.io"
    // Notice: address port is only supported for Android, if not given, default port 21 will be used.
    window.cordova.plugin.ftp.connect('ftp.xfally.github.io', 'username', 'password', function(ok) {
        console.info("ftp: connect ok=" + ok);

        // You can do any ftp actions from now on...
        window.cordova.plugin.ftp.upload('/localPath/localFile', '/remotePath/remoteFile', function(percent) {
            if (percent == 1) {
                console.info("ftp: upload finish");
            } else {
                console.debug("ftp: upload percent=" + percent * 100 + "%");
            }
        }, function(error) {
            console.error("ftp: upload error=" + error);
        });

    }, function(error) {
        console.error("ftp: connect error=" + error);
    });
}
```

Please refer to [ftp.js](./www/ftp.js) for all available APIs and usages.

## Notice

1. For iOS, `ftp.connect` will always succeed (even if `username` and `password` are incorrect), but it does NOT mean the later actions, e.g. `ls`... `download` will succeed too! So always check their `errorCallback`.
2. Want to upload/download multiple files? The plugin (Android part) inits just one connection and transmits all files via it. If you use asychronous syntax (e.g. `foreach`) to start multiple upload/download in a short time, it may mess the transfer. Instead, you can try [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) or [async](https://github.com/caolan/async) to transmit one after one.

## Thanks

- The iOS native implementing is based on [GoldRaccoon](https://github.com/albertodebortoli/GoldRaccoon).
- The Android native implementing is based on [ftp4j](http://www.sauronsoftware.it/projects/ftp4j/) jar (LGPL).

## License

Apache License 2.0. Refer to [LICENSE.md](./LICENSE.md) for more info.

