### OMERO blitz Gradle plugin

The _omero-blitz-plugin_ is a gradle plugin that provides users and projects the ability to generate/compile the nessacary files 
required files to use _omero-blitz_

From a high level, blitz-plugin consists of the following tasks/stages:

1. Import `.ome.xml` map files from `org.openmicroscopy:omero-model` (`omero-model.jar`) resources
2. Using the [`omero-dsl-plugin`](https://gitlab.com/openmicroscopy/incubator/omero-dsl), generate `xx.combined` files
3. Process & split `xx.combined` files into chosen languages

### Usage

Build script snippet for use in all Gradle versions:

```groovy
buildscript {
    repositories {
        mavenLocal() // If plugin is locally published
    }
    dependencies {
         classpath 'org.openmicroscopy:blitzplugin:1.0.0'
    }
}

apply plugin: "org.openmicroscopy.blitzplugin"
```

Build script snippet for new, incubating, plugin mechanism introduced in Gradle 2.1:

```groovy
plugins {
    id "org.openmicroscopy.blitzplugin" version "1.0.0"
}
```

### Blitz Plugin Methods

Use the api block to configure the generation of API files with `org.openmicroscopy.blitz.tasks.SplitTask`. 
The API block can contain one or more split tasks, each with its own chosen language for generating API files. 

```groovy
blitz {
    api {
        java {
            language 'java'
            outputDir 'src/generated/interfaces'
        }

        ice {
            language 'ice'
            outputDir 'src/main/slice'
        }
    }
}
```

### Gradle Tasks

| Task name      | Depends On     |
| -------------- | -------------- |
| importMappings |                |
| processCombine | importMappings |

### SplitTask

The `SplitTask` class is responsible for splitting languages from `.combine` files.
It supports the following languages : 
* `java`
* `c++ (cpp)`
* `python`
* `ice`

If you wish to use the `SplitTask` outside of the `blitz {}` scope, you can customise
it's functionality using

```groovy
// Handle headers
task splitCpp(type: SplitTask) {
    language "cpp"
    outputDir "${buildDir}"
    combined fileTree(dir: "${buildDir}", include: '**/*.combined')
    rename '(.*?)I[.]combined', 'omero/model/$1I'
}
```