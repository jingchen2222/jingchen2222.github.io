---
title: Swagger转化为静态文档
date: 2018-09-18 18:21:35
update: 2018-09-18 18:29:00
tags:
- swagger
categories:
- Tool
---


#### 背景概要
公司的spirng项目使用swagger2来生成线上的Restful API文档，现在需要方便的把最新的api同步到公司wiki中。因此，需要把swagger已经生成的api文档格式化成wiki能支持的格式。

### 解决方案

#### 使用swagger-ui生成api文档
这部分工作之前就完成了，需要注意的是，之前的`swagger`注解不够规范，很多参数和返回结果没有定义清晰的schema。

为此，需要加入`ApiModelProperty`对实体(`Request`和`Response`)进行注解和说明。这样生成的API文档才能有清晰的desc。 另外我加入了example，方便前端同学测试。
<!--more-->

```java
public class DemoQueryRequest extends BaseListRequest {

    @ApiModelProperty(value = "媒体渠道英文", example = "sinanewsapp")
    private String appCode;

    @ApiModelProperty(value = "文案模糊匹配", example = "瘦身")
    private String titleLike;

    @ApiModelProperty(value = "创意图数量", example = "2", allowableValues = "{1, 2, 3}")
    private Integer imageCount;

    @ApiModelProperty(value = "行业", example = "医疗健康")
    private String manualType;
    //... 此处省略无数
}
```



#### 使用插件生成静态文档

* 在project的gradle中加入配置

```gradle
compile group: 'io.github.swagger2markup', name: 'swagger2markup', version: '1.3.3'
```

* 选择参考文章中的方法，使用一个UnitTest来生成静态文档，由于有些自定义的操作，所以不采用plugin的方式。

```java
package com._4paradigm.demo..api;

import io.github.swagger2markup.Swagger2MarkupConfig;
import io.github.swagger2markup.Swagger2MarkupConverter;
import io.github.swagger2markup.builder.Swagger2MarkupConfigBuilder;
import io.github.swagger2markup.markup.builder.MarkupLanguage;
import io.swagger.models.Swagger;
import io.swagger.parser.SwaggerParser;
import org.apache.commons.collections.CollectionUtils;
import org.apache.commons.io.FileUtils;
import org.assertj.core.util.Lists;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.nio.file.Paths;
import java.util.Collections;
import java.util.List;

/**
 * generate swagger api docs
 *
 * @author chenjing
 * @create 2018/9/18
 **/
@RunWith(SpringRunner.class)
@SpringBootTest(classes = {DemoApiApplicationContext.class})
public class ApiDocTest {

    private static final String DOC_DIR = "docs/wiki/generated";

    /**
     * use unit test to generate wiki docs
     * @throws Exception
     */
    @Test
    public void generateWikiDocs() throws Exception {
        //    输出Ascii格式
        Swagger2MarkupConfig config = new Swagger2MarkupConfigBuilder()
                .withMarkupLanguage(MarkupLanguage.CONFLUENCE_MARKUP)
                .withGeneratedExamples()
                .build();

        Swagger2MarkupConverter.from(new URL("http://localhost:8065/v2/api-docs"))
                .withConfig(config)
                .build()
                .toFolder(Paths.get(DOC_DIR));


        generateOneWikiDoc();
    }

    /**
     * 读区{@CODE Swagger2MarkupConverter}生成的wiki文档，
     * 加入一些字符串替换逻辑，因为公司的wiki并不支持language=json
     * @param wikiFileName
     * @return
     */
    private List<String> getWikiList(String wikiFileName) {
        try {
            List<String> list = FileUtils.readLines(new File(wikiFileName));
            if (CollectionUtils.isNotEmpty(list)) {
                List<String> filtedList = Lists.newArrayList();
                list.forEach(line -> {
                    if (line.indexOf("{code:language=json}") >= 0) {
                        line = line.replaceAll("code:language=json", "code:language=js");
                    }
                    filtedList.add(line);
                });
                return filtedList;
            } else {
                return Collections.EMPTY_LIST;
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
        return Collections.EMPTY_LIST;
    }

    /**
     * 将Path和Definitions以及Overview文档合并成一个大文档，并添加目录
     */
    private void generateOneWikiDoc() {
        List<String> finalList = Lists.newArrayList();
        String toc = "{toc:maxLevel=3}";
        String splitLine = "----";
        finalList.add(toc);
        finalList.addAll(getWikiList(DOC_DIR + "/overview.txt"));
        finalList.add(splitLine);
        finalList.addAll(getWikiList(DOC_DIR + "/paths.txt"));
        finalList.add(splitLine);
        finalList.addAll(getWikiList(DOC_DIR + "/definitions.txt"));
        finalList.add(splitLine);
        finalList.addAll(getWikiList(DOC_DIR + "/security.txt"));
        finalList.add(splitLine);


        try {
            FileUtils.writeLines(new File(DOC_DIR + "/wiki.txt"), finalList);
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}

```

* 运行unit test
* 至此将在`docs/wiki/generated/`目录下生成wiki.txt
* 将wiki.txt中的内容copy到wiki编辑器中即可


### 参考文献
[使用Swagger2Markup生成静态文档: https://blog.csdn.net/daijiguo/article/details/81350169](https://blog.csdn.net/daijiguo/article/details/81350169)


