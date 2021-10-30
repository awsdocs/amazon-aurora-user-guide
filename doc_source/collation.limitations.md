# Collation limitations and behaviors<a name="collation.limitations"></a>

Babelfish uses the ICU library for collation support\. The following section lists some of the known limitations and behavior variations of Babelfish collations:
+ **Unicode sorting rules**

  SQL Server SQL collations sort Unicode\-encoded data \(`nchar` and `nvarchar`\) one way, but data that isn't Unicode\-encoded \(`char` and `varchar`\) a different way\. Babelfish databases are always UTF\-8 encoded and always apply Unicode sorting rules consistently, regardless of data type\. Thus, the sort order is the same for `char` or `varchar` as it is for `nchar` or `nvarchar`\.
+ **Secondary\-equal collations**

  The default ICU Unicode secondary\-equal \(`CI_AS`\) collation sorts punctuation marks and other nonalphanumeric characters before numeric characters, and numeric characters before alphabetic characters\. However, the order of punctuation and other special characters is different\. 
+ **Tertiary collations**

   SQL collations, such as `SQL_Latin1_General_Pref_CP1_CI_AS`, support the `TERTIARY_WEIGHTS` function and the ability to sort strings that compare equally in a `CI_AS` collation to be sorted uppercase first: `ABC`, `ABc`, `AbC`, `Abc`, `aBC`, `aBc`, `abC`, and finally `abc`\. Thus, the `DENSE_RANK OVER (ORDER BY column)` analytic function assesses these strings as having the same rank but orders them uppercase first within a partition\.

  You can get a similar result with Babelfish by adding a `COLLATE` clause to the `ORDER BY` clause that specifies a tertiary `CS_AS` collation that specifies `@colCaseFirst=upper`\. However, the `colCaseFirst` modifier applies only to strings that are tertiary\-equal \(rather than secondary\-equal like a `CI_AS` collation\)\. Thus, you can't emulate tertiary SQL collations using a single ICU collation\. 

  As a workaround, we recommend that you modify applications that use the `SQL_Latin1_General_Pref_CP1_CI_AS` collation to use the `BBF_SQL_Latin1_General_CP1_CI_AS` collation first\. Then add `COLLATE BBF_SQL_Latin1_General_Pref_CP1_CS_AS` to any `ORDER BY` clause for this column\.
+ PostgreSQL is built with a specific version of ICU and can match at most one version of a collation\. Variations across versions are unavoidable, as are minor variations across time as languages evolve\.
+ **Character expansion**

  A character expansion treats a single character as equal to a sequence of characters at the primary level\. SQL Server's default `CI_AS` collation supports character expansion; ICU collations support character expansion only for accent\-insensitive collations\.

  When character expansion is required, then use a `AI` collation for comparisons\. However, such collations aren't currently supported by the LIKE operator\.
+ **char and varchar encoding**

  When collations that begin with SQL are used for char or varchar data types, the sort order for characters preceding ASCII 127 is determined by the specific code page for that SQL collation\. For SQL collations, strings declared as char or varchar might sort differently than strings declared as nchar or nvarchar\.

  PostgreSQL encodes all strings with the database encoding so converts all characters to UTF\-8 and sorts using Unicode rules\.

  Because SQL collations sort nchar and nvarchar data types using Unicode rules, Babelfish encodes all strings on the server using UTF\-8\. Babelfish sorts nchar and nvarchar strings the same way it sorts char and varchar strings, using Unicode rules\.
+ **Supplementary character**

  The SQL Server functions `NCHAR`, `UNICODE`, and `LEN` support characters for code\-points outside the Unicode Basic Multilingual Plane \(BMP\)\. In contrast, non\-SC collations use surrogate pair characters to handle supplementary characters\. For Unicode data types, SQL Server can represent up to 65,535 characters using UCS\-2, or the full Unicode range \(‭1,114,111‬ characters\) if supplementary characters are used\. 
+ **Kana\-sensitive**

  When Japanese Kana characters `Hiragana` and `Katakana` are treated differently, the collation is called Kana\-sensitive \(KS\)\. ICU supports the Japanese collation standard `JIS X 4061`\. The now deprecated `colhiraganaQ [on | off]` locale modifier might provide the same functionality as KS collations\. However, KS collations of the same name as SQL Server aren't currently supported by Babelfish\.
+ Width\-Sensitive

  When a single\-byte character \(half\-width\) and the same character represented as a double\-byte character \(full\-width\) are treated differently, the collation is called *width\-sensitive \(WS\)*\. WS collations with the same name as SQL Server aren't currently supported by Babelfish\.
+ Variation\-Selector Sensitivity

  Variation\-Selector Sensitivity \(VSS\) collations distinguish between ideographic variation selectors in Japanese collations `Japanese_Bushu_Kakusu_140` and `Japanese_XJIS_140`\. A variation sequence is made up of a base character plus an additional variation selector\. If you don't select the `_VSS` option, the variation selector isn't considered in the comparison\.

  VSS collations aren't currently supported by Babelfish\.
+ BIN and BIN2

  A BIN2 collation sorts characters according to code point order\. The byte\-by\-byte binary order of UTF\-8 preserves Unicode code point order, so this is also likely to be the best\-performing collation\. If Unicode code point order works for an application, consider using a BIN2 collation\. However, using a BIN2 collation can result in data being displayed on the client in an order that is culturally unexpected\. New mappings to lowercase characters are added to Unicode as time progresses, so the `LOWER` function might perform differently on different versions of ICU\. This is a special case of the more general collation versioning problem rather than as something specific to the BIN2 collation\. 

  Babelfish provides the `BBF_Latin1_General_BIN2` collation with the Babelfish distribution to collate in Unicode code point order\. In a BIN collation only the first character is sorted as a wchar\. Remaining characters are sorted byte\-by\-byte, effectively in code point order according to its encoding\. This approach doesn't follow Unicode collation rules and isn't supported by Babelfish\.