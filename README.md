# francetv zoom Utils Slugify with UTest #

This utils method is used to slugify text.
Most of tracking data identifier need to be generated through this method to remove accentuated and others non-alphanumeric chararacters.

This project is part of [francetv zoom open source projects](https://github.com/francetv/zoom-public) (iOS, Android and Angular).

#### Example : ####

"on n'est pas couché !" -> "on-n-est-pas-couche"

#### Utils Java class ####

```java

import java.text.Normalizer;
import java.util.Locale;

public abstract class TagUtils {

    private static final String SLUGIFY_DEFAULT_SEPARATOR = "-";

    public static String slugify(final String value) {
        return slugify(value, SLUGIFY_DEFAULT_SEPARATOR);
    }

    public static String slugify(String value, final String separator) {
        if (TextUtils.isEmpty(value)) {
            return "";
        }
        value =  Normalizer.normalize(value.toLowerCase(Locale.FRANCE), Normalizer.Form.NFD).replaceAll("[\\p{InCombiningDiacriticalMarks}]", "");

        final String[] splited = value.replaceAll(" ", separator)
                .replaceAll("\\\\t", "")
                .replaceAll("\\\\n", "")
                .replaceAll("\\\\r", "")
                .replaceAll("[^\\p{Alnum}]", separator)
                .split(separator);

        final StringBuilder result = new StringBuilder();
        boolean needSep = false;

        for (int i = 0; i<splited.length; i++) {
            if (!TextUtils.isEmpty(splited[i])) {
                if (needSep) {
                    result.append(separator);
                }
                result.append(splited[i]);
                needSep = true;
            }
        }
        return result.toString();
    }
}

```

(You should overide your own TextUtils.isEmpty() method if you have not include Android library in your test environment)

#### JUnit Test ####

```java

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameter;
import org.junit.runners.Parameterized.Parameters;

import java.util.Arrays;
import java.util.Collection;

import static org.junit.Assert.assertEquals;

@RunWith(Parameterized.class)
public class TagUtilsTest {

    @Parameters(name = "{index}: slugify({1})={0}")
    public static Collection<Object[]> data() {
        return Arrays.asList(new Object[][]{
                {"", "", null},
                {"abc-1-2-3", "abc-1-2-3", null},
                {"acegiklnuo", "āčēģīķļņūö", null},
                {"eaetestaaoo", "ÉÀÈTESTÂÄÔÖ", null},
                {"test", "?;::test++-__!!", null},
                {"nice-te-st", "nice ?;::te§st++-__!!", null},
                {"test", "----test------", null},
                {"chinese-text", "chinese 中文測試 text", null},
                {"test", "( test !", null},
                {"test-tip-top", "test tip top", null},
                {"testtiptop", " testtiptop ", null},
                {"test-tip-top", " test   \\n tip   \\t top ", null},
                {"test-tip-top", "test ---- tip - top ", null},
                {"abc123", "abc-1-2-3", ""},
                {"acegiklnuo", "āčēģīķļņūö", ""},
                {"eaetestaaoo", "ÉÀÈTESTÂÄÔÖ", ""},
                {"test", "?;::test++-__!!", ""},
                {"nicetest", "nice ?;::te§st++-__!!", ""},
                {"test", "----test------", ""},
                {"chinesetext", "chinese 中文測試 text", ""},
                {"test", "( test !", ""},
                {"testtiptop", "test tip top", ""},
                {"testtiptop", " testtiptop ", ""},
                {"testtiptop", " test   \\n tip   \\t top ", ""},
                {"testtiptop", "test ---- tip - top ", ""}
        });
    }

    @Parameter
    public String expected;

    @Parameter(value = 1)
    public String input;

    @Parameter(value = 2)
    public String separator;

    @Test
    public void slugify() {
        if (separator == null) {
            assertEquals(expected, TagUtils.slugify(input));
        } else {
            assertEquals(expected, TagUtils.slugify(input, separator));
        }
    }

}
```

#### License ####

```java
/*
 * The MIT License (MIT)
 *
 * Copyright (c) 2016 France Télévisions
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
 * documentation files (the "Software"), to deal in the Software without restriction, including without limitation
 * the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and
 * to permit persons to whom the Software is furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all copies or substantial portions of
 * the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO
 * THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
 * TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */
 ```