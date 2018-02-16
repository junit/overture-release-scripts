# Release Scripts for Overture

This repository contains several scripts that automate parts of the Overture release process as described in the [Release](https://github.com/overturetool/overture/wiki/Release-Process
) wiki page.

## Description of scripts

### Generate the release notes

All of the following should happen inside the developement branch.

Close the current [milestone](https://github.com/overturetool/overture/milestones) and move all the pending [issues](https://github.com/overturetool/overture/issues) to the next milestone. 

[`github-fetch-milestone-issues.py`](https://raw.githubusercontent.com/overturetool/overture-release-scripts/master/github-fetch-milestone-issues.py): Script for generating release notes for *closed* milestones. The template file `ReleaseNotes-template.md` must exist in the folder where the script is. If you are releasing Overture you may want to execute this script from `<overture-root>/documentation/releasenotes`.

Fill in the info in the release notes file, add it to git and commit.

Change versions inside `overture.release.properties` which is located in the root of the tool repository.

Push changes to ensure testing.

### Update the website examples

Run `update-examples.sh` at some tmp folder: Checks out the Overture standard examples, including the website, and adds the examples to the web site. After the script has been executed, the user must manually push the changes to the examples and website repositories. The instructions are provided by the script.

### Perform the release

#### Prepare the artifacts

Run `perform-release.sh`in the root of the tool repository. It performs a Maven release with tycho mode enabled by the `With-IDE` profile. This script can be run in interactive mode, i.e. it stops before pushing/releasing. Alternatively, one can perform an automated release by setting the environment variable `batchmode=release`. Before this script is executed, the release version and the new development version must be specified in the `overture.release.properties` file, which is located in the root of the tool repository. This file must be edited and comitted before running the script. Run script as `./perform-release.sh`.

In order to run this script you will need GPG set up as well as a login to access [Sonatype](http://oss.sonatype.org). For details, see the instructions in the [release notes](https://github.com/overturetool/overture/wiki/Release-Process).

#### Close and release the artifact on Sonatyne
Follow the video instructions in the [Release](https://github.com/overturetool/overture/wiki/Release-Process
) wiki page.

#### Create a release on github
* Creating a new release on GitHub at <https://github.com/overturetool/overture/releases>
* Copy the contents of the previously generated release notes
* Uploading the distribution files (`target/checkout/ide/product/target/products/*.zip`) and commandline jar (`target/checkout/core/commandline/target/Overture-${RELEASE_VER}.jar`) to the GitHub release.
 * `target/checkout/core/commandline/target`
    * `Overture-${RELEASE_VER}.jar`
 * `target/checkout/ide/product/target/product/`
    * `Overture-${RELEASE_VER}-linux.gtk.x86.zip`
    * `Overture-${RELEASE_VER}-linux.gtk.x86_64.zip`
    * `Overture-${RELEASE_VER}-macosx.cocoa.x86_64.zip`
      * Make sure you sign this before uploading it
      * `sign-overture.sh`: Utility script to sign the overture zip for mac. usa as: `$(./sign-overture.sh zip file name)`
    * `Overture-${RELEASE_VER}-win32.win32.x86.zip`
    * `Overture-${RELEASE_VER}-win32.win32.x86_64.zip`
 * Publish the release

#### Release the IDE of Overture

Check out `master` and merge the release tag, push. When that's done trigger the `overture-master` build job on the overture.au.dk build server in order to release the IDE to be available in the p2 updates feature of eclipse. 

#### Hint

If the `maven-gpg-plugin` is complaining that it "Cannot obtain passphrase in batch mode" you may want to supply your passphrase via your local settings (stored in the`settings.xml` file in the `.m2` folder). An example of how this can be done is shown below.

```XML
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!--  Sonatype entries omitted  -->
  <profiles>
    <profile>
      <id>default</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <gpg.passphrase>your-passphrase</gpg.passphrase>
      </properties>
    </profile>
  </profiles>
</settings>

```

### Utility scripts (optional)

`git-set-private-key.sh`: Utility script to configure git to use a specific private key. Use as `$(./git-set-private-key.sh ~/.ssh/id_rsa_custom)`


