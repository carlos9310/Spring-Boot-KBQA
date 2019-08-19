# Spring-Boot-KBQA

以Spring Boot框架为载体，通过集成hanLP、neo4j、spark-mllib实现基于电影知识图谱的简易问答系统。

首先启动springboot后在浏览器中访问8080端口，接着在网页上输入关于电影的一些问题，前端页面通过AJAX请求将问题发送到后端接口，后端接收到请求后，先加载问题模板、字典、分类模型及自定义字典；再对问题分词后利用分类模型将原问题匹配到对应的问题模板上；最后针对不同种类的问题从图数据库neo4j中查询对应的答案并返回。

# 数据
- mysql (/data/movie_data_import.sql)
- neo4j (先将mysql的数据导出csv文件，再导入到neo4j中，有利于比较两种数据库的关系，图数据库更适合对关系的处理。也可直接将/data/import.rar压缩包内的文件直接导入到neo4j中)
    ```

    找到neo4j的安装路径，并在D:\neo4j-community-3.4.0\目录下创建import目录
    完整路径如下D:\neo4j-community-3.4.0\import
    因为neo4j支持导入csv文件，其默认目录入口是 ...\import


    //导入节点 电影类型  == 注意类型转换
    LOAD CSV WITH HEADERS  FROM "file:///genre.csv" AS line
    MERGE (p:Genre{gid:toInteger(line.gid),name:line.gname})


    //导入节点 演员信息
    LOAD CSV WITH HEADERS FROM 'file:///person.csv' AS line
    MERGE (p:Person { pid:toInteger(line.pid),birth:line.birth,
    death:line.death,name:line.name,
    biography:line.biography,
    birthplace:line.birthplace})


    // 导入节点 电影信息
    LOAD CSV WITH HEADERS  FROM "file:///movie.csv" AS line
    MERGE (p:Movie{mid:toInteger(line.mid),title:line.title,introduction:line.introduction,
    rating:toFloat(line.rating),releasedate:line.releasedate})


    // 导入关系 actedin  电影是谁参演的 1对多
    LOAD CSV WITH HEADERS FROM "file:///person_to_movie.csv" AS line
    match (from:Person{pid:toInteger(line.pid)}),(to:Movie{mid:toInteger(line.mid)})
    merge (from)-[r:actedin{pid:toInteger(line.pid),mid:toInteger(line.mid)}]->(to)

    //导入关系  电影是什么类型 == 1对多
    LOAD CSV WITH HEADERS FROM "file:///movie_to_genre.csv" AS line
    match (from:Movie{mid:toInteger(line.mid)}),(to:Genre{gid:toInteger(line.gid)})
    merge (from)-[r:is{mid:toInteger(line.mid),gid:toInteger(line.gid)}]->(to)
    ```
- 问题模板 (/data/question)
- hanLP的数据 (https://github.com/hankcs/HanLP/releases 中的新数据包data-for-1.7.4.zip)
- 自定义词典 (/data/自定义词典.zip解压后放到hanLP的相关目录下，具体路径参考/src/main/resources/application.properties)


# windows下spark环境

- [https://pan.baidu.com/s/1ZIsh5yRChR0zAJXnUui4jw](https://pan.baidu.com/s/1ZIsh5yRChR0zAJXnUui4jw)

# 参考
- [基于电影知识图谱的智能问答系统（八） -- 终极完结篇](https://blog.csdn.net/appleyk/article/details/80422055)