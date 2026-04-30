# 结果查询
select s1_0.ID,
a1_0.SINGER_ID,a1_0.ID,
a1_0.AWARD_NAME,
a1_0.TYPE,a1_0.ITEM_NAME,
a1_0.VERSION,a1_0.YEAR,
s1_0.BIRTH_DATE,
s1_0.FIRST_NAME,
s1_0.GENRE,
i1_0.SINGER_ID,
i1_1.INSTRUMENT_ID,
s1_0.LAST_NAME,
s1_0.PSEUDONYM,
s1_0.VERSION
from SINGER s1_0
left join AWARD a1_0 on s1_0.ID=a1_0.SINGER_ID
left join (SINGER_INSTRUMENT i1_0 join INSTRUMENT i1_1 on i1_1.INSTRUMENT_ID=i1_0.INSTRUMENT_ID) on s1_0.ID=i1_0.SINGER_ID;
select s1_0.INSTRUMENT_ID,s1_1.ID,s1_1.BIRTH_DATE,s1_1.FIRST_NAME,s1_1.GENRE,s1_1.LAST_NAME,s1_1.PSEUDONYM,s1_1.VERSION
from SINGER_INSTRUMENT s1_0
join SINGER s1_1 on s1_1.ID=s1_0.SINGER_ID
where s1_0.INSTRUMENT_ID=?;
select s1_0.INSTRUMENT_ID,s1_1.ID,s1_1.BIRTH_DATE,s1_1.FIRST_NAME,s1_1.GENRE,s1_1.LAST_NAME,s1_1.PSEUDONYM,s1_1.VERSION
from SINGER_INSTRUMENT s1_0
join SINGER s1_1 on s1_1.ID=s1_0.SINGER_ID
where s1_0.INSTRUMENT_ID=?;
select s1_0.INSTRUMENT_ID,s1_1.ID,s1_1.BIRTH_DATE,s1_1.FIRST_NAME,s1_1.GENRE,s1_1.LAST_NAME,s1_1.PSEUDONYM,s1_1.VERSION
from SINGER_INSTRUMENT s1_0
join SINGER s1_1 on s1_1.ID=s1_0.SINGER_ID
where s1_0.INSTRUMENT_ID=?;
select s1_0.INSTRUMENT_ID,s1_1.ID,s1_1.BIRTH_DATE,s1_1.FIRST_NAME,s1_1.GENRE,s1_1.LAST_NAME,s1_1.PSEUDONYM,s1_1.VERSION
from SINGER_INSTRUMENT s1_0
join SINGER s1_1 on s1_1.ID=s1_0.SINGER_ID
where s1_0.INSTRUMENT_ID=?;
清单 16-23
具有多对多关系的 GraphQL 查询
```

那么，发生了什么？嗯，JPA 查询生成并没有那么智能。如果一名 SQL 开发人员要编写一个查询，从通过多对多关系（如本应用程序中所示）相互关联的表中提取数据，那么该查询将类似于清单 16-24 中所示的查询。

```
select
s1_0.ID,a1_0.SINGER_ID,a1_0.ID,
a1_0.AWARD_NAME,
a1_0.YEAR,
s1_0.BIRTH_DATE,
s1_0.FIRST_NAME,
s1_0.LAST_NAME,
I.INSTRUMENT_ID
from SINGER s1_0
left join AWARD a1_0 on s1_0.ID=a1_0.SINGER_ID
left join SINGER_INSTRUMENT SI on s1_0.ID = SI.SINGER_ID
left join INSTRUMENT I on I.INSTRUMENT_ID = SI.INSTRUMENT_ID;
清单 16-24
具有多对多关系的 SQL 查询
```

此外，如果你仔细查看生成的查询，可能会注意到，无论你在 GraphQL 查询中指定了哪些字段，当为数据库生成查询时，除非你显式编写一个 SQL 原生查询来指定要获取数据的列名，否则 JPA 将生成一个包含所有列的 SQL 查询。从这个例子中，你可能会得出结论：GraphQL 并不像宣传的那样高效。不过，它仍然足够高效，足以让 Facebook 成为全球使用最广泛的社交网络。

让我们看看我们还能支持哪些其他 GraphQL 查询。例如，如果我们想要获取具有特定 `id` 的歌手的全部详细信息，那么要编写的查询可能类似于清单 16-25 中所示的查询。

```
query {
singerById(id: 1){
firstName
lastName
awards {
awardName
year
}
instruments{
name
}
}
}
清单 16-25
用于检索 id 为 1 的歌手的详细信息的 GraphQL 查询
```

为了支持这个查询，我们需要为其添加模式和处理程序方法。此外，这也是介绍 `@Argument` 注解的好机会。清单 16-26 展示了查询模式和处理程序方法。

```
type Query {
singerById(id: ID!) : Singer
}
package com.apress.prospring6.sixteen.boot.controllers;
import org.springframework.graphql.data.method.annotation.Argument;
// 其他导入语句已省略
@Controller
public class SingerController {
@QueryMapping
public Singer singerById(@Argument Long id, DataFetchingEnvironment environment){
Specification spec = byId(id);
DataFetchingFieldSelectionSet s = environment.getSelectionSet();
if (s.contains("awards") && !s.contains("instruments"))
spec =  spec.and(fetchAwards());
else if (s.contains("awards") && s.contains("instruments"))
spec = spec.and(fetchAwards().and(fetchInstruments()));
else if (!s.contains("awards") && s.contains("instruments"))
spec = spec.and(fetchInstruments());
return singerRepo.findOne(spec).orElse(null);
}
// 其他方法已省略
}
清单 16-26
用于检索 id 为 1 的歌手的详细信息的 GraphQL 处理程序方法（在 resources/graphql/singer.graphqls 中声明的 GraphQL 查询）
```

Spring GraphQL 的 `@Argument` 注解将一个命名的 GraphQL 参数绑定到方法参数上。相同的 JPA `Specification<Singer>` 实例也用于为单个歌手加载延迟关系提供支持。

到目前为止，我们只进行了数据检索，因此是时候介绍如何支持诸如创建新的 `Singer` 对象以及更新和删除现有 `Singer` 对象之类的操作了。所有这些操作都通过一个称为 `Mutation` 的概念或类型来支持。与 `Query` 类似，`Mutation` 为创建、更新或删除操作声明模式。这些操作的 GraphQL 查询被映射到使用 `@MutationMapping` 注解的处理程序方法。

清单 16-27 展示了创建和更新操作所需的 Mutation 和 `SingerInput` 类型。

```
type Mutation {
updateSinger(id: ID!, singer: SingerInput): Singer!
deleteSinger(id: ID!): ID!
}
input SingerInput {
firstName: String!
lastName: String!
pseudonym: String
genre: String
birthDate: String
}
package com.apress.prospring6.sixteen.boot.controllers;
import org.springframework.graphql.data.method.annotation.MutationMapping;
// 其他导入语句已省略
@Controller
public class SingerController {
// 其他方法已省略
@MutationMapping
public Singer newSinger(@Argument SingerInput singer){
LocalDate date;
try {
date = LocalDate.parse(singer.birthDate(), DateTimeFormatter.ofPattern("yyyy-MM-dd"));
} catch (DateTimeParseException e) {
throw new IllegalArgumentException("错误的日期格式");
}
var newSinger =  new Singer(null, 0, singer.firstName,singer.lastName,
singer.pseudonym, singer.genre, date, null, null);
return singerRepo.save(newSinger);
}
@MutationMapping
public Singer updateSinger(@Argument Long id, @Argument SingerInput singer) {
var fromDb = singerRepo.findById(id).orElseThrow(() -> new NotFoundException(Singer.class, id ));
fromDb.setFirstName(singer.firstName);
fromDb.setLastName(singer.lastName);
fromDb.setPseudonym(singer.pseudonym);
fromDb.setGenre(singer.genre);
LocalDate date;
try {
date = LocalDate.parse(singer.birthDate(), DateTimeFormatter.ofPattern("yyyy-MM-dd"));
fromDb.setBirthDate(date);
} catch (DateTimeParseException e) {
throw new IllegalArgumentException("错误的日期格式");
}
return singerRepo.save(fromDb);
}
@MutationMapping
public Long deleteSinger(@Argument Long id) {
singerRepo.findById(id).orElseThrow(() -> new NotFoundException(Singer.class, id ));
singerRepo.deleteById(id);
return id;
}
record SingerInput(String firstName, String lastName, String pseudonym, String genre, String birthDate){}
}
清单 16-27
用于检索 id 为 1 的歌手的详细信息的 GraphQL 处理程序方法（在 resources/graphql/singer.graphqls 中声明的 GraphQL 查询）
```



![](img/315511_6_En_16_Figc_HTML.jpg)一个三角形渐变感叹号图标的插图。 在当前版本的 GraphQL 中，你不能声明一个没有返回类型的查询或变更。一种变通方法是在处理方法中返回一个代表操作成功的典型值，例如 `0`（零）或 `OK`，或者直接返回 `null`，并在 schema 中声明一个可空类型。

用于创建、更新和删除歌手的 GraphQL 查询如代码清单 16-28 所示。

```
