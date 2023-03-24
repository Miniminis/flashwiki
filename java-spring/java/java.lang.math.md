# java Math 함수 정리

{% embed url="https://docs.oracle.com/javase/8/docs/api/java/lang/Math.html" %}

```java
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.assertThat;

/**
 * https://docs.oracle.com/javase/8/docs/api/java/lang/Math.html
 * */
public class MathTest {

    @Test
    void abs() {
        assertThat(Math.abs(10)).isEqualTo(10);
        assertThat(Math.abs(-10)).isEqualTo(10);
    }

    @Test
    void ceil() {
        assertThat(Math.ceil(10.123)).isEqualTo(11);
        assertThat(Math.ceil(10.56)).isEqualTo(11);
    }

    @Test
    void floor() {
        assertThat(Math.floor(10.39)).isEqualTo(10);
        assertThat(Math.floor(10.56)).isEqualTo(10);
    }

    @Test
    void round() {
        assertThat(Math.round(10.39)).isEqualTo(10);
        assertThat(Math.round(10.56)).isEqualTo(11);
    }

    @Test
    void max() {
        assertThat(Math.max(10, 100)).isEqualTo(100);
    }

    @Test
    void min() {
        assertThat(Math.min(10, 100)).isEqualTo(10);
    }

    @Test
    void pow() {    //거듭제곱
        assertThat(Math.pow(2, 4)).isEqualTo(16);
    }

    @Test
    void sqrt() {
        assertThat(Math.sqrt(25)).isEqualTo(5);
        assertThat(Math.sqrt(26)).isEqualTo(5.0990195135927845);
    }

    @Test
    void addExact() {
        assertThat(Math.addExact(10, 30)).isEqualTo(40);
        assertThat(Math.addExact(30, 10)).isEqualTo(40);
    }

    @Test
    void subtractExact() {
        assertThat(Math.subtractExact(10, 30)).isEqualTo(-20);
        assertThat(Math.subtractExact(30, 10)).isEqualTo(20);
    }

    @Test
    void decrementExact() {
        assertThat(Math.decrementExact(10)).isEqualTo(9);
        assertThat(Math.decrementExact(318)).isEqualTo(317);
    }

    @Test
    void multiplyExact() {
        assertThat(Math.multiplyExact(10, 20)).isEqualTo(200);
    }

    @Test
    void toIntExact() {
        assertThat(Math.toIntExact(128902389L)).isEqualTo(128902389);
    }
}
```
