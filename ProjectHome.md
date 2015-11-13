This is a struts2 plugin project. It provides a "json" result type that serializes beans into JSON. The serialization process heavily utilize Flexjson(http://flexjson.sourceforge.net/) to be easily configured.

This plugin is very simliar to Struts 2 JSON Plugin(http://cwiki.apache.org/S2PLUGINS/json-plugin.html), the differences are:
  * No interceptor is provided by this plugin, only the json result type is provided.
  * more powerful include/exclude patterns are supported(thanks to the features of flexjson)

The initiative of this project is not to reinvent the wheel. Just because I met some odd problems when integrating spring, struts2, the struts2json plugin and hibernate together.

See the wiki page for documentation.