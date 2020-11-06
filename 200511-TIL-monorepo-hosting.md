## MonoRepo 따로 firebase 호스팅하기

Angular 프로젝트에 새로운 application을 생성했다면 angular.json과 firebase.json을 작성해서 호스팅 설정을 해주면 된다.

* angular.json

```json
"projects": {
  "knox-board": {
    "projectType": "application",
    "schematics": {
      "@schematics/angular:component": {
        "style": "scss"
      }
    },
    "root": "",
    "sourceRoot": "src",
    "prefix": "app",
    "architect": {
      "build": {
        "builder": "@angular-devkit/build-angular:browser",
        "options": {
          "outputPath": "public",
          "index": "src/index.html",
          "main": "src/main.ts",
          "polyfills": "src/polyfills.ts",
          "tsConfig": "tsconfig.app.json",
          "aot": true,
          "assets": [
            "src/favicon.ico",
            "src/assets"
          ],
          "styles": [
            "./node_modules/@angular/material/prebuilt-themes/indigo-pink.css",
            "src/styles.scss"
          ],
          "scripts": []
        },
        "configurations": {
          "production": {
            "fileReplacements": [
              {
                "replace": "src/environments/environment.ts",
                "with": "src/environments/environment.prod.ts"
              }
            ],
            "optimization": true,
            "outputHashing": "all",
            "sourceMap": false,
            "extractCss": true,
            "namedChunks": false,
            "extractLicenses": true,
            "vendorChunk": false,
            "buildOptimizer": true,
            "budgets": [
              {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
              },
              {
                "type": "anyComponentStyle",
                "maximumWarning": "6kb",
                "maximumError": "10kb"
              }
            ]
          },
          "dev": {
            "fileReplacements": [
              {
                "replace": "src/environments/environment.ts",
                "with": "src/environments/environment.dev.ts"
              }
            ],
            "optimization": true,
            "outputHashing": "all",
            "sourceMap": false,
            "extractCss": true,
            "namedChunks": false,
            "extractLicenses": true,
            "vendorChunk": false,
            "buildOptimizer": true,
            "budgets": [
              {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
              },
              {
                "type": "anyComponentStyle",
                "maximumWarning": "6kb",
                "maximumError": "10kb"
              }
            ]
          }
        }
      },
      "serve": {
        "builder": "@angular-devkit/build-angular:dev-server",
        "options": {
          "browserTarget": "knox-board:build"
        },
        "configurations": {
          "production": {
            "browserTarget": "knox-board:build:production"
          }
        }
      },
      "extract-i18n": {
        "builder": "@angular-devkit/build-angular:extract-i18n",
        "options": {
          "browserTarget": "knox-board:build"
        }
      },
      "test": {
        "builder": "@angular-devkit/build-angular:karma",
        "options": {
          "main": "src/test.ts",
          "polyfills": "src/polyfills.ts",
          "tsConfig": "tsconfig.spec.json",
          "karmaConfig": "karma.conf.js",
          "assets": [
            "src/favicon.ico",
            "src/assets"
          ],
          "styles": [
            "./node_modules/@angular/material/prebuilt-themes/indigo-pink.css",
            "src/styles.scss"
          ],
          "scripts": []
        }
      },
      "lint": {
        "builder": "@angular-devkit/build-angular:tslint",
        "options": {
          "tsConfig": [
            "tsconfig.app.json",
            "tsconfig.spec.json",
            "e2e/tsconfig.json"
          ],
          "exclude": [
            "**/node_modules/**"
          ]
        }
      },
      "e2e": {
        "builder": "@angular-devkit/build-angular:protractor",
        "options": {
          "protractorConfig": "e2e/protractor.conf.js",
          "devServerTarget": "knox-board:serve"
        },
        "configurations": {
          "production": {
            "devServerTarget": "knox-board:serve:production"
          }
        }
      },
      "deploy": {
        "builder": "@angular/fire:deploy",
        "options": {}
      }
    }
  },
  "admin": {
    "projectType": "application",
    "schematics": {
      "@schematics/angular:component": {
        "style": "scss"
      }
    },
    "root": "projects/admin",
    "sourceRoot": "projects/admin/src",
    "prefix": "app",
    "architect": {
      "build": {
        "builder": "@angular-devkit/build-angular:browser",
        "options": {
          "baseHref": "/admin/",
          "outputPath": "public/admin",
          "index": "projects/admin/src/index.html",
          "main": "projects/admin/src/main.ts",
          "polyfills": "projects/admin/src/polyfills.ts",
          "tsConfig": "projects/admin/tsconfig.app.json",
          "aot": true,
          "assets": [
            "projects/admin/src/favicon.ico",
            "projects/admin/src/assets"
          ],
          "styles": [
            "./node_modules/@angular/material/prebuilt-themes/indigo-pink.css",
            "projects/admin/src/styles.scss"
          ],
          "scripts": []
        },
        "configurations": {
          "production": {
            "fileReplacements": [
              {
                "replace": "projects/admin/src/environments/environment.ts",
                "with": "projects/admin/src/environments/environment.prod.ts"
              }
            ],
            "optimization": true,
            "outputHashing": "all",
            "sourceMap": false,
            "extractCss": true,
            "namedChunks": false,
            "extractLicenses": true,
            "vendorChunk": false,
            "buildOptimizer": true,
            "budgets": [
              {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
              },
              {
                "type": "anyComponentStyle",
                "maximumWarning": "6kb",
                "maximumError": "10kb"
              }
            ]
          },
          "dev": {
            "fileReplacements": [
              {
                "replace": "projects/admin/src/environments/environment.ts",
                "with": "projects/admin/src/environments/environment.dev.ts"
              }
            ],
            "optimization": true,
            "outputHashing": "all",
            "sourceMap": false,
            "extractCss": true,
            "namedChunks": false,
            "extractLicenses": true,
            "vendorChunk": false,
            "buildOptimizer": true,
            "budgets": [
              {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
              },
              {
                "type": "anyComponentStyle",
                "maximumWarning": "6kb",
                "maximumError": "10kb"
              }
            ]
          }
        }
      },
      "serve": {
        "builder": "@angular-devkit/build-angular:dev-server",
        "options": {
          "browserTarget": "admin:build"
        },
        "configurations": {
          "production": {
            "browserTarget": "admin:build:production"
          }
        }
      },
      "extract-i18n": {
        "builder": "@angular-devkit/build-angular:extract-i18n",
        "options": {
          "browserTarget": "admin:build"
        }
      },
      "test": {
        "builder": "@angular-devkit/build-angular:karma",
        "options": {
          "main": "projects/admin/src/test.ts",
          "polyfills": "projects/admin/src/polyfills.ts",
          "tsConfig": "projects/admin/tsconfig.spec.json",
          "karmaConfig": "projects/admin/karma.conf.js",
          "assets": [
            "projects/admin/src/favicon.ico",
            "projects/admin/src/assets"
          ],
          "styles": [
            "projects/admin/src/styles.scss"
          ],
          "scripts": []
        }
      },
      "lint": {
        "builder": "@angular-devkit/build-angular:tslint",
        "options": {
          "tsConfig": [
            "projects/admin/tsconfig.app.json",
            "projects/admin/tsconfig.spec.json",
            "projects/admin/e2e/tsconfig.json"
          ],
          "exclude": [
            "**/node_modules/**"
          ]
        }
      },
      "e2e": {
        "builder": "@angular-devkit/build-angular:protractor",
        "options": {
          "protractorConfig": "projects/admin/e2e/protractor.conf.js",
          "devServerTarget": "admin:serve"
        },
        "configurations": {
          "production": {
            "devServerTarget": "admin:serve:production"
          }
        }
      }
    }
  }
},
```

​	

admin의 architect - build -baseHref 를 설정해주는 게 중요하다. 나는 knox-board를 `knox-board.wab.app`에 호스팅하고, admin 페이지를 `knoox-board.web.app/admin`에 호스팅하고 싶었다.



* firebase.json

  ```json
  {
    "hosting": [
      {
        "target": "knox-board",
        "public": "public",
        "ignore": [
          "firebase.json",
          "**/.*",
          "**/node_modules/**"
        ],
        "rewrites": [
          {
            "source": "/admin",
            "destination": "/admin/index.html"
          },
          {
            "source": "/admin/**",
            "destination": "/admin/index.html"
          },
          {
            "source": "**",
            "destination": "/index.html"
          }
        ]
      },
      {
        "target": "dev-knox-board",
        "public": "public",
        "ignore": [
          "firebase.json",
          "**/.*",
          "**/node_modules/**"
        ],
        "rewrites": [
          {
            "source": "/admin/**",
            "destination": "/admin/index.html"
          },
          {
            "source": "**",
            "destination": "/index.html"
          }
        ]
      }
    ]
  }
  ```

  rewrite에 `/admin/`의 destination 설정을 해줬다.

  각각 build하고 firebase deploy를 해주면 호스팅이 잘 된다.

  

  관리자 애플리케이션에서 원래 관리자 애플리케이션에서 사용하는 service를 공용으로 이용하고 싶은데 의존성을 낮추면서 코드를 재사용할 방법을 고민해봐야겠다.

