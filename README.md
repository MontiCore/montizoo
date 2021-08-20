<!-- (c) https://github.com/MontiCore/monticore -->
# MontiZoo

The goal of this project is to combine different language projects 
based on MontiCore, developed as individual Gradle projects in 
individual Git repositories but dependent on each other via project 
dependencies, into one project and combine their individual builds into 
one overall build. For this purpose, this project utilizes Git 
submodules in combination with Gradle's composite builds. 

## Quick start
```console
git clone --recurse-submodules https://github.com/MontiCore/montizoo.git
cd montizoo
git submodule foreach -q --recursive 'git checkout $(git config -f $toplevel/.gitmodules submodule.$name.branch || echo master)'
git submodule update --remote --merge
gradle
```

## Submodules 

Submodules can be understood as independent git repositories that are 
integrated into the local project. For example the project MontiCore 
itself and several languages e.g. from UML and SysML are integrated. 
The currently configured submodules can be found in the 
[.gitmodules](.gitmodules) file and are also reflected in the top level 
folders. Important or useful actions are briefly explained below for 
more details explanations please refer to the 
[documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules). 

### Cloning 

When cloning the project make sure to initialize and update the 
submodules as well. An easy way to do this is adding 
`--recurse-submodules` as follows: 

```
git clone --recurse-submodules https://github.com/MontiCore/montizoo.git
```

In case the repository was already cloned without `--recurse-submodules` use the following:
```
git submodule update --init --recursive
```

To track the latest changes of submodules execute:
```
git submodule foreach -q --recursive 'git checkout $(git config -f $toplevel/.gitmodules submodule.$name.branch || echo master)'
```

### Updating
To pull the latest changes of the submodules the following command can be used:

```
git submodule update --remote --merge
```
 or 
```
git submodule update --remote --rebase
```

depending on your desired strategy to integrate submodule changes. If 
you forget to add `--merge` or `--rebase` your project will be in a 
detached HEAD state. From the git documentation: 
> If this happens, don’t worry, you can simply go back into the directory and 
> check out your branch again (which will still contain your work) and merge or 
> rebase [...] manually. 



## Composite builds

Composite builds allows combining Gradle builds that are developed 
independently as well as decompose large multi-project builds. In the 
following the basics are explained briefly for a more detailed 
explanation consider reading the 
[documentation](https://docs.gradle.org/current/userguide/composite_builds.html). 

In this project composite builds are used to combine the individual 
builds of the MontiCore project and a multitude of language projects 
based on MontiCore. All projects that are integrated into this project 
via submodules have their own individual build processes. The composite 
mechanism is used to combine these build processes into one overall 
build process. The builds of the individual submodules are integrated 
into the entire project via the [settings.gradle](settings.gradle) 
file. 

The builds of each submodule are independent, but there are usage 
relationships between the submodules. For example, the language 
projects use MontiCore to generate the language infrastructure from the 
respective grammar. But also the language projects partly use each 
other. In composite builds, Gradle replaces such external dependencies 
with the local builds. This replacement is version number independent, 
so that the latest local implementation is always used. This enables 
changes in the projects to be tested directly in other projects that 
use these projects. 


### Build it

To build the whole project including all its participating projects 
simply call 

```
gradle
``` 

which calls the default task `buildAll` defined in the 
[build.gradle](build.gradle). Make sure that Gradle is installed locally,
as this project does not ship with a Gradle wrapper.


