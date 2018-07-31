# docker02
git demo

## 1. Setup
This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 1.6.5.

### Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

### Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

### Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `-prod` flag for a production build.

### Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

### Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

### Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).


## 2. Auto-highlight of the sections
It uses [angular-contents](https://www.npmjs.com/package/angular-contents).
Basically, it’s a plain typescripts. Find [the examples & docs](https://github.com/zurfyx/angular-contents).

```sh
$ npm i -S @ng-bootstrap/ng-bootstrap
$ npm i -S jquery@2.2.4
$ npm i -S select2
$ npm i -S ng2-select2

$ npm i -S angular-contents
$ npm i -S ngx-page-scroll
```

## 3. Add local environment with mock objects.
* environment configuration file in `src/environments/environment.local.ts`.
* mock objects in `src/app/shared/mocks/` folder.

Add these lines in `.angular-cli.json`:
```json
{
    "styles": [
        "styles.css",
        "../node_modules/select2/dist/css/select2.min.css",
        "app/shared/css/foundation.min.css",
        "app/shared/css/ehe-transitional.css"
    ],
    "scripts": [
        "../node_modules/jquery/dist/jquery.min.js",
        "../node_modules/select2/dist/js/select2.full.min.js"
    ],
    "environments": {
        "dev": "environments/environment.ts",
        "prod": "environments/environment.prod.ts",
        "local": "environments/environment.local.ts"
      }
}
```

Run it with command below:
```sh
$ ng serve --port=8091 --env=local --app=1 --proxy-config=proxy.conf.json
$ ng e2e --no-serve

$ ng build -op C:\Users\rwibawa\eclipse-workspace\PostBilling\WebContent\js\healthHistory\angular
$ ng test --app local --environment local
$ ng e2e --port=8091 --env=local --app=1 --proxy-config=proxy.conf.json

C:>SET PATH=%PATH%;%NVM_HOME%;%NVM_SYMLINK%

# Protractor commands:
C:>node node_modules\protractor\bin\webdriver-manager --help
C:>node node_modules\protractor\bin\webdriver-manager version
C:>node node_modules\protractor\bin\webdriver-manager status
C:>node node_modules\protractor\bin\webdriver-manager update --chrome
C:>node node_modules\protractor\bin\webdriver-manager update --ignore_ssl --ie
C:>node node_modules\protractor\bin\protractor --help
C:>node node_modules\protractor\bin\protractor --version

# start the selenium server
C:>node node_modules\protractor\bin\webdriver-manager start
# on other cmd
C:>node node_modules\protractor\bin\protractor

C:>node node_modules\protractor\bin\webdriver-manager clean
C:>node node_modules\protractor\bin\webdriver-manager update --versions.standalone=3.4.0 --ie64 --versions.ie=3.4.0
C:>node node_modules\protractor\bin\webdriver-manager start --versions.standalone=3.4.0 --versions.ie=3.4.0 --versions.chrome=2.38 --versions.gecko=v0.20.1 --edge=node_modules/protractor/node_modules/webdriver-manager/selenium/MicrosoftWebDriver.exe
# on other cmd
C:>node node_modules\protractor\bin\protractor protractor.conf.js

# check drivers' version
C:>dir node_modules\protractor\node_modules\webdriver-manager\selenium
05/18/2018  04:42 PM         6,405,632 chromedriver_2.38.exe
05/18/2018  04:42 PM         9,684,296 geckodriver-v0.20.1.exe
05/18/2018  04:42 PM         2,729,984 IEDriverServer3.4.0.exe
05/17/2018  01:56 PM           142,560 MicrosoftWebDriver.exe

```

Open it in the browser at [http://localhost:8091](http://localhost:8093).

Notes:
> To run the e2e tests with Chromium (Headless Chrome), enable the _chromeOptions_ in `protractor.conf.js`.
> Download the [Microsoft Edge WebDriver](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/) with correct build number. Put it in `node_modules\protractor\node_modules\webdriver-manager\selenium\MicrosoftWebDriver.exe`.

## 6. Puppeteer (Headless Chrome)
```sh
$ npm i -D puppeteer
$ node epms.js
```

## 5. Update `@angular/cli` from 1.6.5 to 6.0.1
```sh
C:\>npm i -D @angular/cli@6.0.1

> @angular/cli@6.0.1 postinstall C:\Users\rwibawa\Documents\workspaces\EHE Angular\HealthHistory\node_modules\@angular\cli
> node ./bin/ng-update-message.js
================================================================================
The Angular CLI configuration format has been changed, and your existing configuration can
be updated automatically by running the following command:

  ng update @angular/cli
================================================================================

C:\>ng update @angular/cli --migrate-only --from=1.6.5
            Updating karma configuration
            Updating configuration
            Removing old config file (.angular-cli.json)
            Writing config file (angular.json)
            Some configuration options have been changed, please make sure to update any npm scripts which you may have modified.
DELETE .angular-cli.json
CREATE angular.json (8683 bytes)
UPDATE karma.conf.js (1503 bytes)
UPDATE src/tsconfig.spec.json (303 bytes)
UPDATE package.json (1577 bytes)
UPDATE tslint.json (2998 bytes)

C:\>ng serve --port=8091 --project=local -c local --proxy-config=proxy.conf.json
C:\>ng serve local --port=8091 -c local --proxy-config=proxy.conf.json
C:\>ng e2e local-e2e
```
