# Collation limitations and behavior differences<a name="collation.limitations"></a>

Babelfish uses the ICU library for collation support\. PostgreSQL is built with a specific version of ICU and can match at most one version of a collation\. Variations across versions are unavoidable, as are minor variations across time as languages evolve\. In the following list you can find known limitations and behavior variations of Babelfish collations:
+ **Indexes and collation type dependency** – An index on a user\-defined type that depends on the International Components for Unicode \(ICU\) collation library \(the library used by Babelfish\) isn't invalidated when the library version changes\.
+ **COLLATIONPROPERTY function** – Collation properties are implemented only for the supported Babelfish BBF collations\. For more information, see the [Babelfish supported collations table](babelfish-collations.md#bfish-collations-table)\.
+ **Unicode sorting rule differences** – SQL collations for SQL Server sort Unicode\-encoded data \(`nchar` and `nvarchar`\) differently than data that's not Unicode\-encoded \(`char` and `varchar`\)\. Babelfish databases are always UTF\-8 encoded and always apply Unicode sorting rules consistently, regardless of data type, so the sort order for `char` or `varchar` is the same as it is for `nchar` or `nvarchar`\.
+ **Secondary\-equal collations and sorting behavior** – The default ICU Unicode secondary\-equal \(`CI_AS`\) collation sorts punctuation marks and other nonalphanumeric characters before numeric characters, and numeric characters before alphabetic characters\. However, the order of punctuation and other special characters is different\. 
+ **Tertiary collations, workaround for ORDER BY** – SQL collations, such as `SQL_Latin1_General_Pref_CP1_CI_AS`, support the `TERTIARY_WEIGHTS` function and the ability to sort strings that compare equally in a `CI_AS` collation to be sorted uppercase first: `ABC`, `ABc`, `AbC`, `Abc`, `aBC`, `aBc`, `abC`, and finally `abc`\. Thus, the `DENSE_RANK OVER (ORDER BY column)` analytic function assesses these strings as having the same rank but orders them uppercase first within a partition\.

  You can get a similar result with Babelfish by adding a `COLLATE` clause to the `ORDER BY` clause that specifies a tertiary `CS_AS` collation that specifies `@colCaseFirst=upper`\. However, the `colCaseFirst` modifier applies only to strings that are tertiary\-equal \(rather than secondary\-equal such as with `CI_AS` collation\)\. Thus, you can't emulate tertiary SQL collations using a single ICU collation\. 

  As a workaround, we recommend that you modify applications that use the `SQL_Latin1_General_Pref_CP1_CI_AS` collation to use the `BBF_SQL_Latin1_General_CP1_CI_AS` collation first\. Then add `COLLATE BBF_SQL_Latin1_General_Pref_CP1_CS_AS` to any `ORDER BY` clause for this column\.
+ **Character expansion** – A character expansion treats a single character as equal to a sequence of characters at the primary level\. SQL Server's default `CI_AS` collation supports character expansion\. ICU collations support character expansion for accent\-insensitive collations only\.

  When character expansion is required, then use a `AI` collation for comparisons\. However, such collations aren't currently supported by the LIKE operator\.
+ **char and varchar encoding** – When SQL collations are used for `char` or `varchar` data types, the sort order for characters preceding ASCII 127 is determined by the specific code page for that SQL collation\. For SQL collations, strings declared as `char` or `varchar` might sort differently than strings declared as `nchar` or `nvarchar`\.

  PostgreSQL encodes all strings with the database encoding, so all characters are converted to UTF\-8 and sorted using Unicode rules\.

  Because SQL collations sort nchar and nvarchar data types using Unicode rules, Babelfish encodes all strings on the server using UTF\-8\. Babelfish sorts nchar and nvarchar strings the same way it sorts char and varchar strings, using Unicode rules\.
+ **Supplementary character** – The SQL Server functions `NCHAR`, `UNICODE`, and `LEN` support characters for code\-points outside the Unicode Basic Multilingual Plane \(BMP\)\. In contrast, non\-SC collations use surrogate pair characters to handle supplementary characters\. For Unicode data types, SQL Server can represent up to 65,535 characters using UCS\-2, or the full Unicode range \(1,114,114 characters\) if supplementary characters are used\. 
+ **Kana\-sensitive \(KS\) collations** – A Kana\-sensitive \(KS\) collation is one that treats `Hiragana` and `Katakana` Japanese Kana characters differently\. ICU supports the Japanese collation standard `JIS X 4061`\. The now deprecated `colhiraganaQ [on | off]` locale modifier might provide the same functionality as KS collations\. However, KS collations of the same name as SQL Server aren't currently supported by Babelfish\.
+ **Width\-sensitive \(WS\) collations** – When a single\-byte character \(half\-width\) and the same character represented as a double\-byte character \(full\-width\) are treated differently, the collation is called *width\-sensitive \(WS\)*\. WS collations with the same name as SQL Server aren't currently supported by Babelfish\.
+ **Variation\-selector sensitive \(VSS\) collations** – Variation\-selector sensitive \(VSS\) collations distinguish between ideographic variation selectors in Japanese collations `Japanese_Bushu_Kakusu_140` and `Japanese_XJIS_140`\. A variation sequence is made up of a base character plus an additional variation selector\. If you don't select the `_VSS` option, the variation selector isn't considered in the comparison\.

  VSS collations aren't currently supported by Babelfish\.
+ **BIN and BIN2 collations** – A BIN2 collation sorts characters according to code point order\. The byte\-by\-byte binary order of UTF\-8 preserves Unicode code point order, so this is also likely to be the best\-performing collation\. If Unicode code point order works for an application, consider using a BIN2 collation\. However, using a BIN2 collation can result in data being displayed on the client in an order that is culturally unexpected\. New mappings to lowercase characters are added to Unicode as time progresses, so the `LOWER` function might perform differently on different versions of ICU\. This is a special case of the more general collation versioning problem rather than as something specific to the BIN2 collation\. 

  Babelfish provides the `BBF_Latin1_General_BIN2` collation with the Babelfish distribution to collate in Unicode code point order\. In a BIN collation only the first character is sorted as a wchar\. Remaining characters are sorted byte\-by\-byte, effectively in code point order according to its encoding\. This approach doesn't follow Unicode collation rules and isn't supported by Babelfish\.
+ **Non\-deterministic collations and CHARINDEX limitation** – For Babelfish releases older than version 2\.1\.0, you can't use CHARINDEX with non\-deterministic collations\. By default, Babelfish uses a case\-insensitive \(non\-deterministic\) collation\. Using CHARINDEX for older versions of Babelfish raises the following runtime error:

  ```
  nondeterministic collations are not supported for substring searches
  ```
**Note**  
This limitation and workaround apply to Babelfish version 1\.x only \(Aurora PostgreSQL 13\.x versions\)\. Babelfish 2\.1\.0 and higher releases don't have this issue\.

  You can work around this issue in one of the following ways:
  + Explicitly convert the expression to a case\-sensitive collation and case\-fold both arguments by applying LOWER or UPPER\. For example, `SELECT charindex('x', a) FROM t1` would become the following:

    ```
    SELECT charindex(LOWER('x'), LOWER(a COLLATE sql_latin1_general_cp1_cs_as)) FROM t1
    ```
  + Create a SQL function f\_charindex, and replace CHARINDEX calls with calls to the following function:

    ```
    CREATE function f_charindex(@s1 varchar(max), @s2 varchar(max)) RETURNS int
    AS
    BEGIN
    declare @i int = 1
    WHILE len(@s2) >= len(@s1)
    BEGIN
      if LOWER(@s1) = LOWER(substring(@s2,1,len(@s1))) return @i
      set @i += 1
      set @s2 = substring(@s2,2,999999999)
    END
    return 0
    END
    go
    ```