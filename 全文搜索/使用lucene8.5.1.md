这里是一个简单的demo,包含创建索引,索引简单的搜索
# pom文件
```xml
<properties>
        <lunece.version>8.5.1</lunece.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!-- lucene核心库 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- Lucene的查询解析器 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-queryparser</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- lucene的默认分词器库 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-analyzers-common</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- lucene的高亮显示 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-highlighter</artifactId>
            <version>${lunece.version}</version>
        </dependency>

        <!-- luncene中文分词器-->
        <dependency>
            <groupId>com.jianggujin</groupId>
            <artifactId>IKAnalyzer-lucene</artifactId>
            <version>8.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-memory</artifactId>
            <version>${lunece.version}</version>
        </dependency>
    </dependencies>
```

# Indexer.java

```java
public class Indexer {
    @Test
    //创建索引
    public void createIndex() throws IOException {
        //1. 指定索引存放路径
        Directory index  = FSDirectory.open(Paths.get("D:\\lucene\\20200508"));

        //2. 指定写索引相关的配置,指定中文分词器
        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new IKAnalyzer());
        IndexWriter indexWriter = new IndexWriter(index, indexWriterConfig);

        //3. 创建文档对象
        Document doc = new Document();

        //4.为document添加field,并对数据建立索引
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(getClass().getClassLoader().getResourceAsStream("doc.txt")));

        String line = null;
        while((line= bufferedReader.readLine()) != null){
            String[] split = line.split(",");
            doc.add(new Field("id", split[0], TextField.TYPE_STORED));
            doc.add(new Field("email", split[1], TextField.TYPE_STORED));
            doc.add(new Field("content", split[2], TextField.TYPE_STORED));
            doc.add(new Field("attach", String.valueOf(split[3]), TextField.TYPE_STORED));
            doc.add(new Field("name", split[4], TextField.TYPE_STORED));
            indexWriter.addDocument(doc);
        }
        bufferedReader.close();
        indexWriter.close();
        index.close();
    }

    @Test
    public void openIndex()throws Exception{
        //1. 指定索引存放路径
        Directory index  = FSDirectory.open(Paths.get("D:\\lucene\\20200508"));
        //2. 打开索引用于搜索
        DirectoryReader reader = DirectoryReader.open(index);
        System.out.println("存储文档的数量" + reader.numDocs());
        System.out.println("存储最大文档的数量" + reader.maxDoc());
        System.out.println("删除的文档数量" + reader.numDeletedDocs());
    }

    @Test
    public void searchByTerm() throws Exception{
        Directory index = FSDirectory.open(Paths.get("D:\\lucene\\20200508"));
        IndexSearcher searcher =  new IndexSearcher(DirectoryReader.open(index));

        Query query = new TermQuery(new Term("email", "qq"));
        TopDocs tds = searcher.search(query, 2);

        System.out.println("一共查询了:" + tds.totalHits);
        for (ScoreDoc scoreDoc : tds.scoreDocs) {
            Document doc = searcher.doc(scoreDoc.doc);
            List<IndexableField> fields = doc.getFields();
            for (IndexableField indexableField : fields) {
                System.out.println(indexableField.name()+ ":" +doc.get(indexableField.name()));
            }
            System.out.println("----------------------------------");
        }
    }
}
```

