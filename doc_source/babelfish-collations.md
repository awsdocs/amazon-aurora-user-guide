# Babelfish collation support<a name="babelfish-collations"></a>

A *collation* specifies the sort order and presentation format of data\. Babelfish maps SQL Server collations to comparable collations provided by Babelfish\. Babelfish predefines Unicode collations with culturally sensitive string comparisons and sort orders\. Babelfish also provides a way to translate the collations in your SQL Server DB to the closest\-matching Babelfish collation\. Locale\-specific collations are provided for different languages and regions\.

Some collations specify a code page that corresponds to a client\-side encoding\. Babelfish automatically translates from the server encoding to the client encoding depending on the collation of each output column\.

Babelfish uses version 153\.80 of the ICU collation library\. For detailed information about PostgreSQL collation behavior, see [the PostgreSQL documentation](https://www.postgresql.org/docs/13/collation.html)\. 

Babelfish supports deterministic and nondeterministic collations:
+ A *deterministic collation* considers two characters as equal if they have the exact same byte sequence\. A deterministic collation evaluates `x` and `X` as not equal\. Collations that are deterministic are case\-sensitive \(CS\) and accent\-sensitive \(AS\)\.
+ A *nondeterministic collation* doesn't require an identical match\. A nondeterministic collation evaluates `x` and `X` as equal\. Nondeterministic collations are case\-insensitive \(CI\) and accent\-insensitive \(AI\)\.

Babelfish and SQL Server follow a naming convention for collations that describe the collation attributes, as shown in the table following\.


| Attribute | Description | 
| --- | --- | 
| AI | Accent\-insensitive\. | 
| AS | Accent\-sensitive\. | 
| BIN2 | BIN2 requests data to be sorted in code point order\. Unicode code point order is the same character order for UTF\-8, UTF\-16, and UCS\-2 encodings\. Code point order is a fast deterministic collation\. | 
| CI | Case\-insensitive\. | 
| CS | Case\-sensitive\. | 
| PREF | To sort uppercase letters before lowercase letters, use a PREF collation\. If comparison is case\-insensitive, the uppercase version of a letter sorts before the lowercase version, if there is no other distinction\. The ICU library supports uppercase preference with `colCaseFirst=upper`, but not for CI\_AS collations\. PREF can be applied only to `CS_AS` deterministic collations\. | 

PostgreSQL doesn't support the LIKE clause on nondeterministic collations, but Babelfish supports it for CI\_AS collations\. Babelfish doesn't support the `LIKE` clause on AI collations\. Pattern matching operations on nondeterministic collations also aren't supported\.

To establish Babelfish collation behavior, set the following parameters\.


| Parameter | Description | 
| --- | --- | 
|  `default_locale`  |  The ` default_locale ` parameter is used in combination with the collation attributes in the table preceding to customize collations for a specific language and region\. The default value is `en-US`\.  The default locale applies to all Babelfish collations that start with the letters BBF, and to all SQL Server collations that are mapped to Babelfish collations\. You can change this parameter after initial Babelfish database creation, but it doesn't affect the locale of existing collations\.  | 
|  `server_collation_name`  |  The collation used as the default collation at both the server level and the database level\. The default value is `sql_latin1_general_cp1_ci_as`\. The `server_collation_name` has to be a `CI_AS` collation because in T\-SQL, the server collation determines how identifiers are compared\. You can choose from the collations in the table that follows for the `Collation name` field when you create your Aurora PostgreSQL cluster for use with Babelfish\. Don't modify the `server_collation_name` after the Babelfish database is created\.  | 

Use the following collations as a server collation or an object collation\.


| Collation ID | Notes | 
| --- | --- | 
|  BBF\_Unicode\_General\_CI\_AS  |  Supports case\-insensitive comparison and the LIKE operator\.  | 
|  BBF\_Unicode\_CP1\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1252.txt) also known as CP1252\.  | 
|  BBF\_Unicode\_CP1250\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1250.txt) used to represent texts in Central European and Eastern European languages that use Latin script\.  | 
|  BBF\_Unicode\_CP1251\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1251.txt) for languages that use the Cyrillic script\.  | 
|  BBF\_Unicode\_CP1253\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1253.txt) used to represent modern Greek\.  | 
|  BBF\_Unicode\_CP1254\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1254.txt) that supports Turkish\.  | 
|  BBF\_Unicode\_CP1255\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1255.txt) that supports Hebrew\.  | 
|  BBF\_Unicode\_CP1256\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1256.txt) used to write languages that use Arabic script\.  | 
|  BBF\_Unicode\_CP1257\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1257.txt) used to support Estonian, Latvian, and Lithuanian languages\.  | 
|  BBF\_Unicode\_CP1258\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1258.txt) used to write Vietnamese characters\.  | 
|  BBF\_Unicode\_CP874\_CI\_AS  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit874.txt) used to write Thai characters\.  | 
|  sql\_latin1\_general\_cp1250\_ci\_as  |  [Nondeterministic single\-byte character encoding](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1250.txt) used to represent Latin characters\.  | 
|  sql\_latin1\_general\_cp1251\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1251.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1252.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1253\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1253.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1254\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1254.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1255\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1255.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1256\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1256.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1257\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1257.txt) that supports Latin characters\.  | 
|  sql\_latin1\_general\_cp1258\_ci\_as  |  [Nondeterministic collation](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/bestfit1258.txt) that supports Latin characters\.  | 
|  chinese\_prc\_ci\_as  |  Nondeterministic collation that supports Chinese \(PRC\)\.  | 
|  cyrillic\_general\_ci\_as  |  Nondeterministic collation that supports Cyrillic\.  | 
|  finnish\_swedish\_ci\_as  |  Nondeterministic collation that supports Finnish\.  | 
|  french\_ci\_as  |  Nondeterministic collation that supports French\.  | 
|  korean\_wansung\_ci\_as  |  Nondeterministic collation that supports Korean \(with dictionary sort\)\.  | 
|  latin1\_general\_ci\_as  |  Nondeterministic collation that supports Latin characters\.  | 
|  modern\_spanish\_ci\_as  |  Nondeterministic collation that supports Modern Spanish\.  | 
|  polish\_ci\_as  |  Nondeterministic collation that supports Polish\.  | 
|  thai\_ci\_as  |  Nondeterministic collation that supports Thai\.  | 
|  traditional\_spanish\_ci\_as  |  Nondeterministic collation that supports Spanish \(traditional sort\)\.  | 
|  turkish\_ci\_as  |  Nondeterministic collation that supports Turkish\.  | 
|  ukrainian\_ci\_as  |  Nondeterministic collation that supports Ukrainian\.  | 
|  vietnamese\_ci\_as  |  Nondeterministic collation that supports Vietnamese\.  | 

You can use the following collations as object collations\.


| Dialect | Deterministic options | Nondeterministic options | 
| --- | --- | --- | 
|  Arabic  |  Arabic\_CS\_AS  |  Arabic\_CI\_AS, Arabic\_CI\_AI  | 
|  Chinese  |  Chinese\_CS\_AS  |  Chinese\_CI\_AS, Chinese\_CI\_AI  | 
|  Cyrillic\_General  |  Cyrillic\_General\_CS\_AS  |  Cyrillic\_General\_CI\_AS, Cyrillic\_General\_CI\_AI  | 
|  Estonian  |  Estonian\_CS\_AS  |  Estonian\_CI\_AS, Estonian\_CI\_AI  | 
|  Finnish\_Swedish  |  Finnish\_Swedish\_CS\_AS  |  Finnish\_Swedish\_CI\_AS, Finnish\_Swedish\_CI\_AI  | 
|  French  |  French\_CS\_AS  |  French\_CI\_AS, French\_CI\_AI  | 
|  Greek  |  Greek\_CS\_AS  |  Greek\_CI\_AS, Greek\_CI\_AI  | 
|  Hebrew  |  Hebrew\_CS\_AS  |  Hebrew\_CI\_AS, Hebrew\_CI\_AI  | 
|  Korean\_Wamsung  |  Korean\_Wamsung\_CS\_AS  |  Korean\_Wamsung\_CI\_AS, Korean\_Wamsung\_CI\_AI  | 
|  Modern\_Spanish  |  Modern\_Spanish\_CS\_AS  |  Modern\_Spanish\_CI\_AS, Modern\_Spanish\_CI\_AI  | 
|  Mongolian  |  Mongolian\_CS\_AS  |  Mongolian\_CI\_AS, Mongolian\_CI\_AI  | 
|  Polish  |  Polish\_CS\_AS  |  Polish\_CI\_AS, Polish\_CI\_AI  | 
|  Thai  |  Thai\_CS\_AS  |  Thai\_CI\_AS, Thai\_CI\_AI  | 
|  Traditional\_Spanish  |  Traditional\_Spanish\_CS\_AS  |  Traditional\_Spanish\_CI\_AS, Traditional\_Spanish\_CI\_AI  | 
|  Turkish  |  Turkish\_CS\_AS  |  Turkish\_CI\_AS, Turkish\_CI\_AI  | 
|  Ukranian  |  Ukranian\_CS\_AS  |  Ukranian\_CI\_AS, Ukranian\_CI\_AI  | 
|  Vietnamese  |  Vietnamese\_CS\_AS  |  Vietnamese\_CI\_AS, Vietnamese\_CI\_AI  | 