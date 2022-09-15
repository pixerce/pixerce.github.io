---
layout: default
title: "junit - @TestInstance"
date: 2022-09-15 15:16:16 +0900
categories: test
---

Spring Batch에는 job의 상태나 결과 등의 정보를 저장하는 meta-table이라는게 있다.   
테이블명은 BATCH_ 로 시작하는데 Boot에서는 Properties에서 설정하여 자동으로 테이블 생성이 가능하지만, boot가 아니라면 임의로 생성해야 한다.   

```XML
<batch:job-repository id="jobRepository" table-prefix="BATCH_TEST_TABLE_" />
```

meta-tables 은 여러 개 있으며 위 table-prefix가 적용되면,   
BATCH_JOB_INSTANCE -> BATCH_TEST_TABLE_JOB_INSTANCE 로 변경된다.

스프링에는 schema-h2.sql 라고 meta-tables을 생성하는 쿼리가 있어서 해당 파일을 읽어서 테이블명을 변경하도록 개발 했다.
```JAVA
    private Resource createResource(final String schemaClassPath) {
        return new ClassPathResource(schemaClassPath);
    }

    private String createScript(final Resource resource) throws IOException {
        return ScriptUtils.readScript(new LineNumberReader(new BufferedReader(new InputStreamReader(resource.getInputStream())))
                , ScriptUtils.DEFAULT_COMMENT_PREFIX
                , ScriptUtils.DEFAULT_STATEMENT_SEPARATOR
                , ScriptUtils.DEFAULT_BLOCK_COMMENT_END_DELIMITER);
    }

    private List<String> createQueries() throws IOException {

        Resource resource = this.createResource(this.schemaClassPath);

        List<String> queries = new ArrayList<String>();
        ScriptUtils.splitSqlScript( new EncodedResource(resource)
                , this.createScript(resource)
                , ScriptUtils.DEFAULT_STATEMENT_SEPARATOR
                , ScriptUtils.DEFAULT_COMMENT_PREFIX
                , ScriptUtils.DEFAULT_BLOCK_COMMENT_START_DELIMITER
                , ScriptUtils.DEFAULT_BLOCK_COMMENT_END_DELIMITER, queries);

        return queries;
    }

    public void createTables() throws IOException, SQLException {
        final List<String> queries = this.createQueries();

        final Connection connection = DataSourceUtils.getConnection(this.dataSource);
        for (String query : queries) {
            if (this.createPattern.matcher(query).matches() && !StringUtils.isEmpty(this.customizedTablePrefix)) {
                final String newQuery = query.replace(this.defaultTablePrefix, this.customizedTablePrefix);
                connection.createStatement().execute(newQuery);
            }
        }
    }
```

잘 작동해서 다행이다 싶었는데 문제는.. Test Case를 작성하면서 발생했다.   
위 테이블 생성 로직은 테스트 실행 전 1번만 수행하면 되는 것이라서 @BeforeAll에 넣으려고 했다. 하지만 @BeforeAll은 기본적으로 static 메서드에 사용할 수 있으므로 DataSource가 필요한 위 테이블 생성로직과는 맞지 않았다.

혹시나 싶어서 https://junit.org/junit5/docs/current/user-guide/ 을 찾아봤는데, @BeforeAll을 non-static 메서드에서도 사용할 수 있는 방법이 있다.   
Junit의 Test Instance LifeCycle은 테스트 메서드마다 클래스의 인스턴스를 생성하는 방식이다.
즉, 클래스에 테스트 메서드가 5개면 클래스 인스턴스를 5개 생성하는 것으로 예상치 못한 사이드 이펙트로부터 테스트 메서드를 보호하고 개별적인 테스트 메서드 실행 보장하는 장점이 있다.

이러한 Test Instance LifeCycle을 클래스 단위로 생성하도록 변경할 수 있는데 @TestInstance를 사용하는 것이다.
```JAVA
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```

그리고 @BeforeAll을 다음과 깉이 non-static으로 사용할 수 있다.
```JAVA
    @BeforeAll
    public void setup() throws IOException, SQLException {
        new MetaTableFactory("BATCH_TEST_TABLE_", this.dataSource).createTables();
    }
```



