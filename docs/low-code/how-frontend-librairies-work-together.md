# How our frontend libraries work together

We have two main libraries that are used in our frontend applications: `eQualUI` and `sb-shared-lib` which is provided by [`symbiose-ui` repository](https://github.com/yesbabylon/symbiose-ui/tree/dev-2.0). 


```plantuml
@startuml
label "        equal Framework"

cloud eQualUI {
    package eQualUI as eQualUI {
        collections services as eQualUIServices
    }
}

file equal.bundle.js #lightgreen

package "sb-shared-lib" {
    collections services as symbioseUIServices
    collections components as symbioseUIComponents   
}

node "Angular APP (ng)"

node "global node_modules folder" {
    card "/node_modules/sb-shared-lib"
}

eQualUI -[plain]-> "        equal Framework"

eQualUI -[plain]-> equal.bundle.js : compiled file

equal.bundle.js -[plain]-> "/node_modules/sb-shared-lib": "exported to sb-shared-lib                                                   "

"/node_modules/sb-shared-lib" -[plain]-> "Angular APP (ng)" : "npm link sb-shared-lib                      "

"sb-shared-lib" -[plain]-> "global node_modules folder" : npm link

eQualUIServices <-[dotted]- symbioseUIServices : use

symbioseUIServices -[dotted]-> "Angular APP (ng)" : "import                "

@enduml
```