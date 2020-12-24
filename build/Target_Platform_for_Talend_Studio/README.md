# Current target platform
Eclipse used for Target setup: 2020-12

Using:
Eclipse: https://download.eclipse.org/releases/2020-12
Orbit: https://download.eclipse.org/tools/orbit/downloads/drops/R20201130205003/repository/
Jeeyul: https://eclipse.jeeeyul.net/update/
Nebula / Nattable: https://download.eclipse.org/nebula/releases/2.4.3/ / https://download.eclipse.org/nattable/releases/2.0.0/repository/
Eclipse test plugin: https://download.eclipse.org/eclipse/updates/4.18


# How to prepare a target platform for testing in eclipse.

Open the .target in Eclipse using the Target Editor.
Either edit from the target editor, or modify manually.

Note: Seems much faster if we know the list of features needed to edit manually the file to set the version.

Once target is prepared, most simple is to:
Click on button on the top right corder of the target editor in Eclipse, export it to a folder, this will create the p2 target.

Then set as usual the target in eclipse, using folders with:
- Eclipse p2 repository
- tos p2
- ee p2 (if testing ee product)
- bonita p2 (if test bonita/MDM)
- babel p2 (can be included in eclipse p2, just not required for development normally)
tos/ee p2, can be easily built manually / edited to customize the target platform easily. (mvn package in the folder + edit pom to add more dependencies)

It's sometimes more simple to add a new dependency directly from tos p2 target rather than update the eclipse platform itself.
Another point is that Eclipse p2 / tos p2 should be enough normally to check from a development environment for a TOS.


# How to prepare the final target platform to build the studio:


First, Eclipse p2 repository (main one):
- Difficulty here is more to get a target working for all os : macos, linux, windows.
- It needs to export the target platform three times, one for each OS, then rebuild the p2.
- Export first as win32 (Environment: win32/win32/x86_64), in a folder for example /win32
- Export as macos (Environment: macos/cocoa/x86_64), in a folder for example /macos
- Export as linux (Environment: linux/gtk/x86_64), in a folder for example /linux
- Copy / merge all the plugins/features into another folder, for example: /building-target
- Now we need to rebuild the p2 (contents/artifacts), and put it in another folder for example /any, using:

/Applications/Eclipse.app/Contents/MacOS/eclipse -application org.eclipse.equinox.p2.publisher.FeaturesAndBundlesPublisher -metadataRepository file:<basefolder>/any -artifactRepository file:<basefolder>/any -source file:<basefolder>/building-target -config ANY -publishArtifacts -compress

(replace of course <basefolder> with the origin folder)

Once it's done, build can be tested locally (modify pom / set url of folder using file:// as prefix instead of URL)
If everything is ok, target can be deployed to nexus, using for example:

mvn deploy:deploy-file -Dfile=/Applications/java/talend/TargetPlatformE44/p2site/TargetPlatform.zip -DgroupId=org.talend.repo -DartifactId=eclipse-p2-repo -Dversion=4.18.0 -Dpackaging=zip -Durl=https://artifacts-oss.talend.com/nexus/content/repositories/TalendOpenSourceSnapshot -DrepositoryId=talend_nexus_deployment
(prefer use the original version of eclipse instead of talend version)

Nexus p2 repository will be available at https://artifacts-oss.talend.com/nexus/content/unzip/TalendP2UnzipOpenSourceRelease/org/talend/repo/eclipse-p2-repo/4.18.0/eclipse-p2-repo-4.18.0.zip-unzip/