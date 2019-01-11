---
title: Add final and javadoc for auto-generated getters and setters in IntelliJ
date: 2019-01-11 10:29:06
tags:
---

## Default final modifier

To satisfy Checkstyle setup. In IntelliJ (version 2018.3.2) Preferences | Editor > Code Style > Java, select "Code Generation" tab, check "Make generated parameters final"

![Final Modifier](/blog/img/IntelliJ%20-%20final%20modifier.png "Final Modifier")

## javadoc for auto-generated getters and setters

Create customised Getter Template:

``` template
/**
* Gets $field.name.
*
* @return value of $field.name
*/
#if($field.modifierStatic)
static ##
#end
$field.type ##
#set($name = $StringUtil.capitalizeWithJavaBeanConvention($StringUtil.sanitizeJavaIdentifier($helper.getPropertyName($field, $project))))
#if ($field.boolean && $field.primitive)
    is##
#else
    get##
#end
${name}() {
    return $field.name;
}
```

![Getter Template](/blog/img/IntelliJ%20-%20Getter%20Template.png "Getter Template")

Create customised Setter Template:

``` template
/**
* Sets $field.name.
*
* @param $field.name value of $field.name
*/
#set($paramName = $helper.getParamName($field, $project))
#if($field.modifierStatic)
static ##
#end
void set$StringUtil.capitalizeWithJavaBeanConvention($StringUtil.sanitizeJavaIdentifier($helper.getPropertyName($field, $project)))($field.type $paramName) {
#if ($field.name == $paramName)
    #if (!$field.modifierStatic)
    this.##
    #else
        $classname.##
    #end
#end
$field.name = $paramName;
}
```

![Setter Template](/blog/img/IntelliJ%20-%20Setter%20Template.png "Setter Template")

Use the customised Getter and Setter templates when automatically generate getters and setters:

![auto-generated getters setters](/blog/img/IntelliJ%20-%20auto-generated%20getters%20setters.png "auto-generated getters setters")
