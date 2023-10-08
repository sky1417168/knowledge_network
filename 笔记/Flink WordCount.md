### 批处理
```java
import org.apache.flink.api.common.typeinfo.Types;  
import org.apache.flink.api.java.ExecutionEnvironment;  
import org.apache.flink.api.java.operators.AggregateOperator;  
import org.apache.flink.api.java.operators.DataSource;  
import org.apache.flink.api.java.operators.FlatMapOperator;  
import org.apache.flink.api.java.operators.UnsortedGrouping;  
import org.apache.flink.api.java.tuple.Tuple2;  
import org.apache.flink.util.Collector;  
  
public class WordCountDemo {  
    public static void main(String[] args) throws Exception {  
        // 1. 创建执行环境  
        ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();  
        // 2. 从文件读取数据 按行读取(存储的元素就是每行的文本)  
        DataSource<String> lineDS = env.readTextFile("input/words.txt");  
        // 3. 转换数据格式  
        FlatMapOperator<String, Tuple2<String, Long>> wordAndOne =  
                lineDS.flatMap((String line, Collector<Tuple2<String, Long>> out) ->  
                {  
                    String[] words = line.split(" ");  
                    for (String word : words) {  
                        out.collect(Tuple2.of(word, 1L));  
                    }  
                }).returns(Types.TUPLE(Types.STRING, Types.LONG));  
        // 当 Lambda 表达式 使用 Java 泛型的时候, 由于泛型擦除的存在, 需要显示的声明类型信息  
        // 4. 按照 word 进行分组  
        UnsortedGrouping<Tuple2<String, Long>> wordAndOneUG = wordAndOne.groupBy(0);  
        // 5. 分组内聚合统计  
        AggregateOperator<Tuple2<String, Long>> sum = wordAndOneUG.sum(1);  
        // 6. 打印结果  
        sum.print();  
    }  
}
```

### 流处理
socket 文本流数据的发送，可以通过 Linux 系统自带的 netcat 工具进行模拟
```bash
nc -lk 7777
```
WordCount程序
```java
import org.apache.flink.api.common.typeinfo.Types;  
import org.apache.flink.api.java.tuple.Tuple2;  
import org.apache.flink.streaming.api.datastream.DataStreamSource;  
import org.apache.flink.streaming.api.datastream.KeyedStream;  
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;  
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;  
import org.apache.flink.util.Collector;  
  
import java.util.Arrays;  
  
public class WordCountDemo {  
    public static void main(String[] args) throws Exception {  
        // 1. 创建流式执行环境  
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();  
        // 2. 读取文件  
        DataStreamSource<String> lineDSS = env.readTextFile("input/words.txt");  
        // 3. 转换数据格式   
SingleOutputStreamOperator<Tuple2<String, Long>> wordAndOne = lineDSS.flatMap(  
                (String line, Collector<String> words) ->  
                {  
                    Arrays.stream(line.split(" ")).forEach(words::collect);  
                })  
                .returns(Types.STRING).map(word -> Tuple2.of(word, 1L))  
                .returns(Types.TUPLE(Types.STRING, Types.LONG));  
        // 4. 分组   
KeyedStream<Tuple2<String, Long>, String> wordAndOneKS = wordAndOne.keyBy(t -> t.f0);  
        // 5. 求和   
SingleOutputStreamOperator<Tuple2<String, Long>> result = wordAndOneKS.sum(1);  
        // 6. 打印   
result.print();  
        // 7. 执行   
env.execute();  
    }  
}
```
