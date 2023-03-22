# apk_updater-autoinstaller_in_ionic_6
when  new apk version is available application check version and auto upadet the apk.

# Plugin requirements
    Android: 5+

    cordova: 10.0.0+

    cordova-android: 9.0.0+

# Installation
  # Cordova

  cordova plugin add cordova-plugin-apkupdater

  # Ionic + Cordova

  ionic cordova plugin add cordova-plugin-apkupdater

  # Capacitor

  npm install cordova-plugin-apkupdater


# import 


              import { Component, NgZone } from '@angular/core';

              // for file download apk 
              import ApkUpdater, { Progress } from 'cordova-plugin-apkupdater';
              import { Platform, IonRouterOutlet } from '@ionic/angular';
              import { AppVersion } from '@awesome-cordova-plugins/app-version/ngx';
              import { AlertController } from '@ionic/angular';

              @Component({
                selector: 'app-home',
                templateUrl: 'home.page.html',
                styleUrls: ['home.page.scss'],
              })
              export class HomePage {
                progress: number = 0;
                progressBar: boolean = false;
                appVersionNumber: string = "";

                constructor(
                  private platform: Platform,
                  private appVersion: AppVersion,
                  public alertController: AlertController,
                  public _zone: NgZone
                ) {
                  this.deviceReady();
                }



                //  auto download latest app version


                deviceReady() {
                  this.platform.ready().then(() => {
                    if (this.platform.is('android')) {

                      this.appVersion.getVersionNumber().then(response => {
                        this.appVersionNumber = response;
                        var version;

                        // check your app version to latest version
                        if (this.appVersionNumber != version[0].Version) {
                          this.appUpdateConfirm();
                        }

                      }).catch(error => {
                        alert(error);
                      });
                    }
                  });
                }


                async appUpdateConfirm() {
                  const alert = await this.alertController.create({
                    header: 'New version available! ',
                    message: 'Please update new version ',
                    mode: 'ios',
                    backdropDismiss: false,
                    buttons: [
                      {
                        text: 'Update',
                        handler: () => {
                          // this.authanticationSer.removeSession('authData');
                          // window.location.href = "/";
                          this.progressBar = true;
                          this.update();
                        }
                      }]
                  });
                  await alert.present();
                }

                async update() {
                  const installedVersion = (await ApkUpdater.getInstalledVersion());
                  console.log("versions", installedVersion);
                  // server apk rrul here
                  await ApkUpdater.download('https://online.futuregenerali.in/DSR/FgBancaConnectMain.zip', {
                    onDownloadProgress: (e) => {
                      this._zone.run(() => {
                        this.progress = e?.progress ?? 0;
                      });
                      console.log(e.progress, this.progress);
                    },
                    onUnzipProgress: console.log
                  }, async () => {
                    await ApkUpdater.install(console.log, console.error);
                  }, (error) => {
                    console.error(error);
                  }
                  )
                }

              }

# html & css for progressbar 


     <div class="progress-outer" *ngIf="progressBar"  >
        <div class="progress-inner" [style.width]="progress + '%'">
          {{ progress}}%
        </div>
      </div>


      .progress-outer {
      width: 96%;
      margin: 10px 2%;
      padding: 2px;
      text-align: center;
      background-color: #f4f4f4;
      // border: 1px solid #dcdcdc;
      color: #fff;
      border-radius: 10px;
    }

    .progress-inner {
      min-width: 15%;
      white-space: nowrap;
      overflow: hidden;
      padding: 2px;
      border-radius: 10px;
      background-color:var(--main-bg-color);
      font-size: 10px;
    }
