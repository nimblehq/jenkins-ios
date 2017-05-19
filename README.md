# jenkins-ios
Continuous Integration (CI) is a valuable addition to your workflow. Software teams use CI to run a series of scripts or automated tests after each commit to a central repository, to gauge the performance and quality of the codebase.

## Contents: <br/>
[Installing Jenkins](#installing-jenkins)<br />
[XCode project setup](#xcode-project-setup)<br />
[Jenkins plugins](#jenkins-plugins)<br />
[Installing code metrics tools](#installing-code-metrics-tools)<br />
[Setting up the job](#setting-up-the-job)<br />
[Troubleshooting](#troubleshooting)<br />

## Installing Jenkins

Download Jenkins from here: [Install Jenkins for macOS](http://jenkins-ci.org/content/thank-you-downloading-os-x-installer) <br/>
I would recommend that the install should be made under the administrator user and not let the Jenkins installer create it's own user. That will generate issues when trying to access the keychain.

To run Jenkins go to `/Applications/Jenkins` and run the `jenkins.war` file. Make sure you have the latest Java JDK installed.

After that, you can access the Jenkins Dashboard using the following URL: [http://localhost:8080](http://localhost:8080)

## XCode project setup

First, go to your project target from the left top corner of XCode, click on the target and select "Manage Schemes".
There you should check the "Shared" option for your XCode project (do not check shared for the Pods project).<br/>
Target - > Manage schemes - > Check “Shared”

Then, in the project's Build Settings turn on Generate Test Coverage Files and Instrument Program Flow for the Debug configuration on your main target and for both Debug an Release for your test target.

To take advantage of the generated files you need to set the Build Products and Intermediates to be saved in a folder relative to the Workspace. You can do this by accessing XCode Preferences -> Locations -> Custom -> Relative to Workspace.


To check if everything it's OK you can build the project and check the following folders for .gcno and .gcda files
`Build/Intermediates/YOUR-TEST-TARGET-NAME.build/Objects-normal/i386'`
and
`Build/Intermediates/YOUR-MAIN-TARGET-NAME.build/Objects-normal/i386'`
You can find these based on the setting you have in Xcode for Locations.

## Jenkins plugins
To setup you job, you will need the following plugins:

XCode plugin<br />
Git plugin<br />
Keychains and Provisioning Profiles Plugin<br />
Duplicate Code Scanner Plug-in<br />
Cobertura plugin<br />
SLOCCount Plugin<br />
EnvInject Plugin<br />

To add Jenkins plugins you must go to Jenkins -> Manage Jenkins -> Manage plugins.

## Installing code metrics tools
##### Install Homebrew
To install Homebrew, simply copy-paste this code into your Terminal and press enter:

<code>ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" </code>

##### Optional Installing xctool:<br/>`brew install xctool`<br/>for unit testing

## Setting up the job

Here comes the fun part: setting up the Jenkins job.
The first step is creating the job: New item -> Freestyle project. This will let you do all the configuration of a job.

#### Source Code Management
Our project is usually stored in a Git or SVN repository. The Jenkins job begins with this step: cloning the latest version of your branch.
You can setup this by adding your repository info & credentials in the Source Code Management section. For our job, we have a GitHub repostiory: https://github.com/nimbl3/jenkins-ios.git and your GitHub credentials. Then you can specify the branch you want to clone and build, in this case: `*/master`.

#### Build Environment
A Jenkins job does not have access to environment variables such as PATH, that allows us the invoke different scripts and tools without specifing the full path to that script. This scripts are stored at different locations and this variable knows about those locations.

You can check the PATH variableby opening a Terminal window and type `echo $PATH`.<br />
In my case: `/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Server.app/Contents/ServerRoot/usr/bin:/Applications/Server.app/Contents/ServerRoot/usr/sbin:/usr/local/Cellar/`

Next, in the Build Environment section, we will add the line `PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Server.app/Contents/ServerRoot/usr/bin:/Applications/Server.app/Contents/ServerRoot/usr/sbin:/usr/local/Cellar/` in the Properties Content field.

#### XCode build

If everything is fine until this point, you can move on to the buil & distribute part of the job.
Go on and add an XCode build step. This plugin will invoke the xcodebuild command line tool and you can add all the build parameters here. Keep in mind that I'm using CocoaPods.

In this case:
*   Target:                                   JenkinIOSBuildTest
*   Clean before build?                       YES
*   Generate Archive?                         YES
*   Pack application and build .ipa?          YES
*   .ipa filename pattern:                    ${VERSION}
*   Output directory:                         ${workspace}/Builds/${BUILD_NUMBER}/${BUILD_ID}
*   Unlock Keychain?                          YES
*   Keychain path:                            ${HOME}/Library/Keychains/login.keychain
*   Keychain password:                        your administrator user password
*   Xcode Schema File:                        JenkinIOSBuildTest
*   Xcode Workspace File:                     ${WORKSPACE}/JenkinIOSBuildTest
*   Xcode Project Directory:                  ${WORKSPACE}
*   Xcode Project File:                       ${WORKSPACE}/JenkinIOSBuildTest
*   Build output directory:                   ${WORKSPACE}/Build
*   Provide version number and run avgtool?   YES
*   Technical version:                        ${BUILD_ID}


After you configured this to your needs you should check if the job finishes successfully before moving on to the next step.

## License

This project is Copyright (c) 2014-2017 Nimbl3 Ltd. It is free software,
and may be redistributed under the terms specified in the [LICENSE] file.

[LICENSE]: /LICENSE

## About

![Nimbl3](https://dtvm7z6brak4y.cloudfront.net/logo/logo-repo-readme.jpg)

This project is maintained and funded by Nimbl3 Ltd.

We love open source and do our part in sharing our work with the community!
See [our other projects][community] or [hire our team][hire] to help build your product.

[community]: https://github.com/nimbl3
[hire]: https://nimbl3.com/