---
layout: post
title: "PMD静态代码检查"
date: 2012-10-11 11:20
comments: true
categories: maven
tags: [ pmd, maven, eclipse ]
---

## 一 maven插件：maven-pmd-plugin
>pom.xml添加如下内容：

``` xml
		<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-pmd-plugin</artifactId>
				<version>2.7.1</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>check</goal>
						</goals>
					</execution>
				</executions>
				<configuration>
					<failurePriority>2</failurePriority>
					<targetJdk>1.6</targetJdk>
					<rulesets>
						<ruleset>/pmd-rulesets.xml</ruleset>
					</rulesets>
				</configuration>
			</plugin>
```
>failurePriority用于指定在什么错误级别会failure，级别0~5不等。0为最高，5为最低。此处设为2,意为0、1、2级别的错误都会导致报错。级别可以根据项目的要求进行配置
<!--more-->
>其中**pmd-rulesets.xml**是规则文件,由pmd制定的一些规则，这些规则可以在pmd-*.jar里找到。 **pmd-rulesets.xml**类似于以下：

```xml
    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
	<ruleset xmlns="http://pmd.sf.net/ruleset/1.0.0" name="pmd-rulsets" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://pmd.sf.net/ruleset_xml_schema.xsd" xsi:schemaLocation="http://pmd.sf.net/ruleset/1.0.0 http://pmd.sf.net/ruleset_xml_schema.xsd">
  	 	<description>PMD Plugin preferences rule set</description>
   		<rule ref="rulesets/typeresolution.xml/LooseCoupling"/>
   		<rule ref="rulesets/typeresolution.xml/CloneMethodMustImplementCloneable"/>
   		<rule ref="rulesets/typeresolution.xml/SignatureDeclareThrowsException"/>
  		<rule ref="rulesets/braces.xml/IfStmtsMustUseBraces"/>
   		<rule ref="rulesets/braces.xml/WhileLoopsMustUseBraces"/>
	</ruleset>
```

### 如何使用
``` sh
	mvn pmd:pmd
	mvn pmd:check
```
---
## 二 pmd在eclipse中的使用
1.安装
>Help -> Install New Software -> Add...
>设置update site:<http://pmd.sourceforge.net/eclipse>
>一路next安装成功即可

2.eclipse中的设置
>Window -> Prefrences -> PMD -> Rules Configuration
>在其中可以设置相关Rules，这里面的Rules对应maven-pmd-plugins中的pmd-rulesets.xml，可以根据自己的需求进行定制

3.eclipse中的使用
>右键选中project -> PMD -> Check Code with PMD
>执行结束后，会打开PMD视图，会依据不同的Priority级别显示不同的颜色。其中红色标注的X是级别为错误级别的。

---
