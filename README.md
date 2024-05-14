# gitlab-ci-magento
This is a **gitlab deployer** tool inspired from [magento-actions](https://github.com/MAD-I-T/magento-actions) for gitlab-ci users. Install, build, test and deploy magento through gitlab-ci.

<h3>Intro tutorial in video </h3>
<div align="center">
  <a href="https://www.youtube.com/watch?v=NtZTYLooz-g"><img src="https://user-images.githubusercontent.com/3765910/212692624-690325f8-0cad-4ba2-a144-e9b3503a2da8.png" alt="magento zero downtime in video gitlab-ci"></a>
</div>

Also checkout

<h3>The Magento zero downtime deployment <a href="https://www.madit.fr/r/gitlab-deployer-tutorial ">written tutorial</a>.</h3>



Usage
------

Include the [template](https://raw.githubusercontent.com/MAD-I-T/gitlab-ci-magento/v3.28/.magento-actions-full-template.yml) (ie `https://raw.githubusercontent.com/MAD-I-T/gitlab-ci-magento/v3.28/.magento-actions-full-template.yml`) in your `.gitlab-ci.yml`
and extend the of jobs/actions you want to trigger. (more about [include property](https://docs.gitlab.com/ee/ci/yaml/includes.html#include-an-array-of-configuration-files) on gitlab)

Like in **[.gitlab-ci-usage-sample.yml](https://github.com/MAD-I-T/gitlab-ci-magento/blob/main/examples/.gitlab-ci-usage-sample.yml)** this will trigger the build and some test.
The project must respect the following scafolding :

```bash
├── .gitlab.ci
├── README.md 
├── magento # directory where you Magento source files should go 
└── pwa-studio # optional pwa-studio directory for pwa src code
```

You can check the **[.gitlab-ci-install-sample.yml](https://github.com/MAD-I-T/gitlab-ci-magento/blob/main/examples/.gitlab-ci-install-sample.yml)** to help setup the project for you.
For the runner to be able to push the magento and/or pwa-studio directory to the repo. You must declare a deploys key in **Settings>Repository>Deploy Keys**  with a `write` access.
Watch the video below or [read the article](https://www.madit.fr/r/gitlab-deployer-tutorial) to see how to create and use the mandatory gitlab-ci variables (ssh_config, ssh key, etc...). The full list of variables are available in the template file.
Also [see full list of actions](#List-of-available-actions).
<h3>Zero-downtime tutorial in video</h3>
<div align="center">
  <a href="https://www.youtube.com/watch?v=FUxV3w5FLec"><img src="https://user-images.githubusercontent.com/3765910/141300038-43ad383b-af98-4b51-a46a-a4044e2fcbf4.png" alt="magento zero downtime in video gitlab-ci"></a>
</div>


If you're not familiar with CI/CD on gitlab, we do offer hands on trainning and setup, **[see details about the offer here](https://www.madit.fr/shop/product/ci-cd-support-gitlab-ci-magento-9)** 

## List of available actions 

- [install magento from gitlab-ci](#install-magento)
- [install pwa-studio from gitlab-ci](#install-pwa-studio)
- [install magento from mage-os repo](#install-magento-from-mage-os)
- [Magento and/or pwa-studio build](#run-build)
- [Coding standard check](#Magento-coding-standard-check)
- [Magento security scanners files](#Scan-magento-files-for-vulnerabilities)
- [Magento modules security scanner](#Scan-magento-modules-for-vulnerabilities)
- [Unit testing](#Run-all-magento-unit-tests)
- [Zero-downtime-deployer](#Deploy-magento-to-staging-server)
- [Integration tests](#Unit-test-filtered-by-testsuite)
- [Static testing](#Run-all-magento-static-tests)
- [Phpstan](#Run-phpstan)
- [Mess detector](#Run-mess-detector)
- [OVERRIDING THE DEFAULT SCRIPTS](#Overriding)
- [Complex build & deploy strategies](#Complex strategies)
- [see more on the forum](https://forum.madit.fr/)



### Install magento
If you want to push the files to the repo pass INPUT_NO_PUSH to 0.
```
install-magento:
  variables:
    INPUT_MAGENTO_VERSION: "2.4.5-p1"
    INPUT_NO_PUSH: 1
  extends: .install-magento:stage:install
```
One can use our **[magento-devbox](https://github.com/MAD-I-T/magento-devbox)** as local dev env. Linux and mac os supported.
### install PWA-studio
Donwload / Install the latest pwa-studio src
If you want to push the files to the repo pass INPUT_NO_PUSH to 0.
It is also possible to specify a specific pwa-studio version you want through **INPUT_VERSION** variable.
```
pwa-studio:stage:install:
  variables:
    INPUT_NO_PUSH: 1
  extends: .pwa-studio:stage:install
```

### install magento from mage-os
If you want to push the files to the repo pass INPUT_NO_PUSH to 0.
```
install-mage-os-magento:
  variables:
    INPUT_MAGENTO_VERSION: "2.4.5-p1"
    INPUT_NO_PUSH: 1
  extends: .install-mage-os:stage:install
```


### Run build 
Run all magento and/or pwa-studio apps
```
build:
  extends: .build:stage
```
By default the build processor will try and build all available themes in your project.

Nonetheless, check the following link, if you wish to limit the build to specific [theme(s) and/or locale(s)](`https://forum.madit.fr/t/build-magento-specific-theme-s-on-gitlab-ci-hyva-support/99`).
Also [hyva based](https://forum.madit.fr/t/build-magento-specific-theme-s-on-gitlab-ci-hyva-support/99/2) themes are supported.
### Run all magento static tests
```
static-test-all:
  extends: .static-test:stage:test
```

### Run all magento unit tests
```
unit-test-all:
  extends: .unit-test:stage:test
```


### Unit test filtered by testsuite
```
unit-test-testsuite:
  variables:
    INPUT_TESTSUITE: "Magento_Unit_Tests_Other"
  extends: .unit-test-testsuite:stage:test
```

### Unit test filtered by path
```
unit-test-custom-path
  variables:
    INPUT_UNIT_TEST_SUBSET_PATH: 'vendor/magento/module-email/Test/Unit'
  extends: .unit-test-custom-path:stage:test
```


### Magento coding standard check
```
coding-standard:
  variables:
    INPUT_EXTENSION: 'magento/vendor/magento/module-email'
  extends: .coding-standard:test:stage
```

### Run phpstan
```
mess-detector:
  variables:
    INPUT_PROCESS: "phpstan"
    INPUT_MD_PATH: 'magento/vendor/magento/module-email' # path to the src to analyse
  extends: phpstan:test:stage
```

### Run mess detector
```
mess-detector:
  variables:
    INPUT_PROCESS: "mess-detector"
    INPUT_MD_PATH: 'magento/vendor/magento/module-email' # path to the src to mess detect
  extends: .mess-detector:test:stage
```

### Scan magento files for vulnerabilities
```
security-scan-files:
  extends: .security-scan-files:stage:test
```


### Scan magento modules for vulnerabilities
```
security-scan-module:
  extends: .security-scan-module:stage:test
```

### Integration test filtered by testsuite
```
integration-test-testsuite:
  variables:
    INPUT_TESTSUITE: "Memory Usage Tests"
  extends: .integration-test-testsuite:stage:test
```

### Integration test on specific class
```
integration-test-class:
  variables:
    INPUT_INTEGRATION_CLASS: "./testsuite/Magento/MemoryUsageTest.php"
  extends: .integration-test-class:stage:test
```

### Integration test filtered by name
```
integration-test-class-filter:
  variables:
    INPUT_TESTSUITE: "Memory Usage Tests"
    INPUT_INTEGRATION_CLASS: "./testsuite/Magento/MemoryUsageTest.php"
    INPUT_INTEGRATION_FILTER: "testAppReinitializationNoMemoryLeak"
  extends: .integration-test-class-filter:stage:test
```


### Deploy magento to staging server
```
deploy-staging:
  extends: .deploy-staging:stage:deploy
```

### cleanup if the staging deployment fails
```
cleanup-staging:
  extends: .cleanup-staging:stage:cleanup
```

### Deploy magento to prod
 This job is dependent on a build job
```
deploy-production:
  extends: .deploy-production:stage:deploy
```

### cleanup if the production deployment fails
```
cleanup-production:
  extends: .cleanup-production:stage:cleanup
```

### Overriding

One can override the default scripts in the parent project (i.e [magento-actions](https://github.com/MAD-I-T/magento-actions/tree/master/scripts)).
use the `INPUT_OVERRIDE_SETTINGS` variable set it to 1.
You'll also have to create scripts or config dirs in the root of your project.
Here's an example of project scafolding to override the action's default configs
  ```bash
  ├── .gitlab-ci.yml
  ├── README.md 
  └── magento # directory where you Magento source files should go
  └── config # the filenames must be similar to thoses of the action ex: config/integration-test-config.php 
  └── scripts #  ex: scripts/build.sh to override the build behaviour 
  ```

### Complex strategies


Build magento only in a git repo containing magento and pwa-studio directory
```
build:
  variables
    INPUT_MAGENTO_ONLY: "1"
  extends: .build:stage
```
Build pwa-studio only in a git repo containing magento and pwa-studio directory
```
build:
  variables
    INPUT_PWA_STUDIO_ONLY: "1"
  extends: .build:stage
```

deploy magento only in a git repo containing magento and pwa-studio directory
```
deploy-production:
  variables
    INPUT_MAGENTO_ONLY: "1"
  extends: .deploy-production:stage:deploy
```
deploy pwa-studio only in a git repo containing magento and pwa-studio directory
```
deploy-production:
  variables
    INPUT_PWA_STUDIO_ONLY: "1"
  extends: .deploy-production:stage:deploy
```

Deploy magento and pwa-studio to different servers from one repo.


