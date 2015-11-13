#The documentation of struts2 json result

# Introduction #

This struts2 plugin simply provide a new result type: 'json'， as an alternative to [struts2 json plugin](http://cwiki.apache.org/S2PLUGINS/json-plugin.html)


# Dependencies #

Put the following jars together with your struts jar files.
  * common logging, the logger name is the same as the class name
  * [flexjson 1.7](http://sourceforge.net/project/showfiles.php?group_id=194042)

You may also need libraries from struts to compile the source files.

# Installation #

Put the dependencies and the struts2jsonresult-xx.jar to your WEB-INF/lib directory.

# Configuration Example #

In struts2.xml, define a package that extends struts-default and set the result type json as default.
```
	<package name="json" extends="struts-default">
		<result-types>
			<result-type name="json" default="true"
				class="code.google.struts2jsonresult.JSONResult">
				<!-- format the output json -->
				<param name="prettyPrint">true</param>
			</result-type>
		</result-types>		
	</package>

```

Define your action in your struts2.xml
```
<action name="listByStudent" class="articleAction" method="listByStudent">
	<result>
		<!-- serialize the result property of the action -->
		<param name="target">result</param>
		<param name="patterns"> -*.class, -gradeArticle </param>
	</result>
</action>
```

And the java code of your Action file:

```

public class ArticleAction {

	private Article article;

	private Object result;
	public String listByStudent() {

		this.result = ....//find from database, return as an list
		return Action.SUCCESS;
	}

	public Object getResult() {
		return result;
	}

	public Article getArticle() {
		return article;
	}

}

```

# Patterns #

You can set the parameter named "patterns" to include or exclude some properties of the bean to be serialized.

Use '+' as include, and '-' as exclude.
For example
```
<action name="listByStudent" class="articleAction" method="listByStudent">
				<result>
					<param name="patterns"> -*.class, -gradeArticle </param>
				</result>
</action>
```

the patterns  will be translated to
```
new JSONSerializer().exclude("*.class").exclude("gradeArticle").
```

About how to use patterns in flexjson, refer to http://flexjson.sourceforge.net/  and the [javadoc](http://flexjson.sourceforge.net/javadoc/flexjson/JSONSerializer.html) of JSONSerializer

# Parameter of the result type #
|Parameter name|Default value|Description|
|:-------------|:------------|:----------|
|target        |null         |the bean to be serialized, if set to null, the action itself will be serialized. |
|patterns      |null         |Fully support the patterns that can be applied to flexjson. See the above section about "Patterns"|
|prettyPrint   |false        |whether to format the result json string |
|rootName      |null         |if set, it will wrap   the resulting JSON in a javascript object that contains a single field   named rootName. |
|deepSerialize |false        |whether to deep serialize the object, by default flexjson will not serialize the collections in a bean |
|out.contentType|application/json|http reponse content type |
|out.statusCode|200          |http status code of the response, sometimes 500 maybe used to indicate an exception |
|out.characterEncoding|utf-8        |http response character encoding |
|out.gzip      |false        |whether to use gzip to compress the response, to reduce the network transfer load |
|out.noCache   |false        |when set to true, the following 3 headers will be set.   Cache-Control set to no-cache, Expires set to 0  and Pragma set to No-cache  |
|prefix        |false        |when set to true, a prefix "{}&&" would be added before the json result, this is to prevent json attack, see [this blog](http://directwebremoting.org/blog/joe/2007/03/05/json_is_not_as_safe_as_people_think_it_is.html) for the detail |
|callbackParameter|null         |when set, the result will be wrapped with a function. The function name is speicified by this callbackParameter. For example: when callbackParameter is set to callback, http://localhost:9080/spiderx/test/test?callback=alert  will generate a result like alert({....});|

# Exception Handling #


You can define some global exception mapping in your struts.xml. when exception is thrown, an special status code can be used, and the client can deal with different exception type by the status code.

For example:

```
	<package name="json" extends="struts-default">
		<result-types>
			<result-type name="json" default="true"
				class="code.google.struts2jsonresult.JSONResult">
				<param name="target">result</param>
				<param name="out.gzip">true</param>
				<param name="prettyPrint">true</param>
			</result-type>
		</result-types>
		
		<global-results>
			<result name="RuntimeException" type="json">
				<param name="out.statusCode">500</param>
				<param name="target">exception</param>
				<param name="patterns">+class,+message,-*</param>
			</result>
			
			<result name="ApplicationException" type="json">
				<param name="out.statusCode">501</param>
				<param name="target">exception</param>
				<param name="patterns">+class,+message,-*</param>
			</result>			
		</global-results>
		<global-exception-mappings>
			<exception-mapping exception="com.tdyc.spider.ApplicationException"
				result="ApplicationException" />
			<exception-mapping exception="java.lang.RuntimeException"
				result="RuntimeException" />
		</global-exception-mappings>
	</package>
```

Now when an exception occurs, for example a NullPointerException, following json will be the output with a status code 500:

```
  {
 "class": "java.lang.NullPointerException",
    "message": null  
  }
```

**Notice the patterns for flexjson**:

The first expression to match a path that action will be taken thus short circuiting all other expressions defined later.

so for "+class,+message,-`*` ", only the class and message properties are included.

# Credit #

  * [Charlie Hubbard](http://sourceforge.net/users/charliehubbard/) for his flexjson
  * deveoplers of [struts2 json plugin](http://code.google.com/p/jsonplugin/), the ResponseWrapper class borrows a lot from their code.

# Contact #
LRGCC (a) YAHOO(dot)COM(dot)CN
I'm now a senior technical consult in a startup company. My major fields are Java/J2EE software development, training, and technical advocating.