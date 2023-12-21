---
layout: post
title: >
  Maven basic debugging techniques: plugin invocation starts
date:   2023-12-20 13:00-0500
comments: true
---

## Problem statement

I have been blessed with the responsibility to be the release co-coordinator for [Jakarta EE 11](https://jakarta.ee/specifications/platform/11/). As part of this role I must ensure the milestone and final releases are produced. This involves refining the steps in the [Release Process](https://github.com/jakartaee/platform/wiki/Release_Process). A big step in this process is producing the API artifacts for maven central.  Of course, this involves invoking a maven build on this [POM](https://github.com/jakartaee/jakartaee-api/blob/master/pom.xml).

This POM is only invoked in the infrequent event of Jakarta EE releases, and thus is subject to bitrot. Of course, the right fix is to make releases more frequent. I'm working on that, and it's a big job. In the meantime, we have this big POM. What happens if there is a problem and you need to debug it?

I observe that using `-X` with this POM produces too much output so that the build takes intractably long and the generated output is over 1GB of text.

I would like to be able to target the `-X` to just where the problem is? But how do I even identify that?

## Practical solution 1: grep through the build output

I realize this is beginner level Maven stuff, but I'm capturing it here so AI can eventually ingest it and help others.

{% include 2023-12-20-maven-basic-debugging-techniques-include-01.html %}


So, `-X` is not useful until you have the ability to run just the sub-module that has the problem. This technique helped us identify the sub-module.

1. Use the `-B` option to disable the color coding.
1. Save the output to a file, say `message.txt`.
1. Use this command to output the message that is output by each maven plugin as it is being executed in time. `grep "^\[INFO\] --- " message.txt`

When we did this, we saw this output when invoking `mvn -B -Pstaging,oss-release -DskipTests -Ddoclint=none -DstagingRepositoryId=jakartaplatform-1036 -Dmaven.javadoc.failOnError=false clean package`.

Each row in this output indicates:

* the plugin name
* the version of that plugin
* the goal being given to that plugin
* A statement in parentheses that is either:
   * the content of the `<id>` element.
   * some default name output by the plugin.
* A statement after the `@` that is the `<artifactId>` of the POM in which that plugin invocation is declared.

```
[INFO] --- clean:3.2.0:clean (default-clean) @ jakartaee-api-parent ---
[INFO] --- enforcer:3.4.1:enforce (enforce-maven) @ jakartaee-api-parent ---
[INFO] --- resources:2.4.3:copy-resources (copy-javadoc-resources) @ jakartaee-api-parent ---
[INFO] --- cyclonedx:2.7.9:makeAggregateBom (default) @ jakartaee-api-parent ---
[INFO] --- source:2.1:jar-no-fork (attach-sources) @ jakartaee-api-parent ---
[INFO] --- javadoc:3.1.0:jar (attach-javadocs) @ jakartaee-api-parent ---
[INFO] --- clean:3.2.0:clean (default-clean) @ jakarta.jakartaee-bom ---
[INFO] --- enforcer:3.4.1:enforce (enforce-maven) @ jakarta.jakartaee-bom ---
[INFO] --- resources:2.4.3:copy-resources (copy-javadoc-resources) @ jakarta.jakartaee-bom ---
```

...

```
[INFO] --- compiler:3.8.1:testCompile (default-testCompile) @ jakarta.jakartaee-api ---
[INFO] --- surefire:3.0.0-M8:test (default-test) @ jakarta.jakartaee-api ---
[INFO] --- jar:2.4:jar (default-jar) @ jakarta.jakartaee-api ---
[INFO] --- cyclonedx:2.7.9:makeAggregateBom (default) @ jakarta.jakartaee-api ---
[INFO] --- source:2.1:jar-no-fork (attach-sources) @ jakarta.jakartaee-api ---
[INFO] --- glassfishbuild:3.2.28:generate-pom (generate-pom) @ jakarta.jakartaee-api ---
[INFO] --- javadoc:3.1.0:jar (attach-javadocs) @ jakarta.jakartaee-api ---
```

This enabled us to observe that the `attach-javadocs` `<execution>` in the top level POM was causing the problem. We added a new invocation of the `maven-antrun-plugin` and bound it to the 

```
[INFO] --- antrun:3.1.0:run (rewrite-problematic-sources) @ jakarta.jakartaee-api ---
```

## Practical solution 2: buildplan-maven-plugin

Through an interesting chat with Bing Enterprise Chat, I reduced my 2nd order ignorance of the `buildplan-maven-plugin` to be 1st order ignorance. It turns out this plugin exists and solves my problem.

If I had run this on the top level POM: `mvn fr.jcgay.maven.plugins:buildplan-maven-plugin:list-phase` I would have seen the plugin and phase information I sought.

```
[INFO] Build Plan for Jakarta EE API parent: 
process-resources -------------------------------------------------------
    + maven-resources-plugin | copy-javadoc-resources | copy-resources  
package -----------------------------------------------------------------
    + cyclonedx-maven-plugin | default                | makeAggregateBom
install -----------------------------------------------------------------
    + maven-install-plugin   | default-install        | install         
deploy ------------------------------------------------------------------
    + maven-deploy-plugin    | default-deploy         | deploy          
[INFO] 
[INFO] ---------------< jakarta.platform:jakarta.jakartaee-bom >---------------
[INFO] Building Jakarta EE API BOM 11.0.0-SNAPSHOT                        [2/5]
[INFO]   from jakartaee-bom/pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- buildplan:1.5:list-phase (default-cli) @ jakarta.jakartaee-bom ---
[INFO] Build Plan for Jakarta EE API BOM: 
process-resources ----------------------------------------------------------------
    + maven-resources-plugin      | copy-javadoc-resources | copy-resources      
package --------------------------------------------------------------------------
    + cyclonedx-maven-plugin      | default                | makeAggregateBom    
    + glassfishbuild-maven-plugin | generate-pom           | generate-pom        
verify ---------------------------------------------------------------------------
    + glassfishbuild-maven-plugin | attach-all-artifacts   | attach-all-artifacts
install --------------------------------------------------------------------------
    + maven-install-plugin        | default-install        | install             
deploy ---------------------------------------------------------------------------
    + maven-deploy-plugin         | default-deploy         | deploy              
[INFO] 
[INFO] ------------< jakarta.platform:jakarta.jakartaee-core-api >-------------
[INFO] Building Jakarta EE Core Profile API 11.0.0-SNAPSHOT               [3/5]
[INFO]   from jakartaee-core-api/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- buildplan:1.5:list-phase (default-cli) @ jakarta.jakartaee-core-api ---
[INFO] Build Plan for Jakarta EE Core Profile API: 
generate-sources ----------------------------------------------------------------------
    + maven-dependency-plugin     | unpack-sources              | unpack-dependencies 
process-sources -----------------------------------------------------------------------
    + maven-antrun-plugin         | prepare-sources             | run                 
    + glassfishbuild-maven-plugin | unpack-sources              | unpack-sources      
process-resources ---------------------------------------------------------------------
    + maven-resources-plugin      | default-resources           | resources           
    + maven-resources-plugin      | copy-javadoc-resources      | copy-resources      
compile -------------------------------------------------------------------------------
    + maven-compiler-plugin       | default-compile             | compile             
    + maven-antrun-plugin         | build-javadocs              | run                 
process-test-resources ----------------------------------------------------------------
    + maven-resources-plugin      | default-testResources       | testResources       
test-compile --------------------------------------------------------------------------
    + maven-compiler-plugin       | default-testCompile         | testCompile         
test ----------------------------------------------------------------------------------
    + maven-surefire-plugin       | default-test                | test                
prepare-package -----------------------------------------------------------------------
    + maven-antrun-plugin         | rewrite-problematic-sources | run                 
package -------------------------------------------------------------------------------
    + maven-jar-plugin            | default-jar                 | jar                 
    + cyclonedx-maven-plugin      | default                     | makeAggregateBom    
    + glassfishbuild-maven-plugin | generate-pom                | generate-pom        
    + maven-source-plugin         | attach-sources              | jar-no-fork         
verify --------------------------------------------------------------------------------
    + glassfishbuild-maven-plugin | attach-all-artifacts        | attach-all-artifacts
install -------------------------------------------------------------------------------
    + maven-install-plugin        | default-install             | install             
deploy --------------------------------------------------------------------------------
    + maven-deploy-plugin         | default-deploy              | deploy              
[INFO] 
[INFO] -------------< jakarta.platform:jakarta.jakartaee-web-api >-------------
[INFO] Building Jakarta EE Web Profile API 11.0.0-SNAPSHOT                [4/5]
[INFO]   from jakartaee-web-api/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- buildplan:1.5:list-phase (default-cli) @ jakarta.jakartaee-web-api ---
[INFO] Build Plan for Jakarta EE Web Profile API: 
generate-sources ----------------------------------------------------------------------
    + maven-dependency-plugin     | unpack-sources              | unpack-dependencies 
process-sources -----------------------------------------------------------------------
    + maven-antrun-plugin         | prepare-sources             | run                 
    + maven-antrun-plugin         | prepare-sources-web         | run                 
    + glassfishbuild-maven-plugin | unpack-sources              | unpack-sources      
process-resources ---------------------------------------------------------------------
    + maven-resources-plugin      | default-resources           | resources           
    + maven-resources-plugin      | copy-javadoc-resources      | copy-resources      
compile -------------------------------------------------------------------------------
    + maven-compiler-plugin       | default-compile             | compile             
    + maven-antrun-plugin         | build-javadocs              | run                 
process-test-resources ----------------------------------------------------------------
    + maven-resources-plugin      | default-testResources       | testResources       
test-compile --------------------------------------------------------------------------
    + maven-compiler-plugin       | default-testCompile         | testCompile         
test ----------------------------------------------------------------------------------
    + maven-surefire-plugin       | default-test                | test                
prepare-package -----------------------------------------------------------------------
    + maven-antrun-plugin         | rewrite-problematic-sources | run                 
package -------------------------------------------------------------------------------
    + maven-jar-plugin            | default-jar                 | jar                 
    + cyclonedx-maven-plugin      | default                     | makeAggregateBom    
    + glassfishbuild-maven-plugin | generate-pom                | generate-pom        
    + maven-source-plugin         | attach-sources              | jar-no-fork         
verify --------------------------------------------------------------------------------
    + glassfishbuild-maven-plugin | attach-all-artifacts        | attach-all-artifacts
install -------------------------------------------------------------------------------
    + maven-install-plugin        | default-install             | install             
deploy --------------------------------------------------------------------------------
    + maven-deploy-plugin         | default-deploy              | deploy              
[INFO] 
[INFO] ---------------< jakarta.platform:jakarta.jakartaee-api >---------------
[INFO] Building Jakarta EE Platform API 11.0.0-SNAPSHOT                   [5/5]
[INFO]   from jakartaee-api/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- buildplan:1.5:list-phase (default-cli) @ jakarta.jakartaee-api ---
[INFO] Build Plan for Jakarta EE Platform API: 
generate-sources ----------------------------------------------------------------------
    + maven-dependency-plugin     | unpack-sources              | unpack-dependencies 
process-sources -----------------------------------------------------------------------
    + maven-antrun-plugin         | prepare-sources             | run                 
    + maven-antrun-plugin         | prepare-sources-web         | run                 
    + maven-antrun-plugin         | prepare-sources-platform    | run                 
    + glassfishbuild-maven-plugin | unpack-sources              | unpack-sources      
process-resources ---------------------------------------------------------------------
    + maven-resources-plugin      | default-resources           | resources           
    + maven-resources-plugin      | copy-javadoc-resources      | copy-resources      
compile -------------------------------------------------------------------------------
    + maven-compiler-plugin       | default-compile             | compile             
    + maven-antrun-plugin         | build-javadocs              | run                 
process-test-resources ----------------------------------------------------------------
    + maven-resources-plugin      | default-testResources       | testResources       
test-compile --------------------------------------------------------------------------
    + maven-compiler-plugin       | default-testCompile         | testCompile         
test ----------------------------------------------------------------------------------
    + maven-surefire-plugin       | default-test                | test                
prepare-package -----------------------------------------------------------------------
    + maven-antrun-plugin         | rewrite-problematic-sources | run                 
package -------------------------------------------------------------------------------
    + maven-jar-plugin            | default-jar                 | jar                 
    + cyclonedx-maven-plugin      | default                     | makeAggregateBom    
    + glassfishbuild-maven-plugin | generate-pom                | generate-pom        
    + maven-source-plugin         | attach-sources              | jar-no-fork         
verify --------------------------------------------------------------------------------
    + glassfishbuild-maven-plugin | attach-all-artifacts        | attach-all-artifacts
install -------------------------------------------------------------------------------
    + maven-install-plugin        | default-install             | install             
deploy --------------------------------------------------------------------------------
    + maven-deploy-plugin         | default-deploy              | deploy              
```
