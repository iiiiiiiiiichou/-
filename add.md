### 题目

ate eat hhs hds ksd atee ssdfe将以上字符串根据字符长度和字符组成分组。eg:ate与eat一组.

### 实现方法

```
  // ate eat hhs hds ksd
            string[] strs = { "ate", "ate", "hhs", "hds", "ksd" };
            List<string> strings = strs.ToList();
            Dictionary<string, List<string>> stringGroups = new Dictionary<string, List<string>>();  
            foreach (string item in strings)
            {
                char[] charStr = item.ToCharArray();
                Array.Sort(charStr);
                String newStr = new String(charStr);

                if (stringGroups.ContainsKey(newStr))
                    stringGroups[newStr].Add(item);
                else
                {
                    List<string> list = new List<string>();
                    list.Add(item);
                    stringGroups.Add(newStr, list);
                }
            }
            foreach (var key in stringGroups.Keys)
            {
                Console.WriteLine(key);
                foreach (var item in stringGroups[key])
                {
                    Console.WriteLine(item);
                    Console.ReadLine();
                }
                
            }
        }
```



### 知识点

1. Array工具类 

   声明，包含，连接，合并，转换，翻转，移除

2. String类构造方法

   字符串本身功能：包含、拼接、截取、相等、索引

   转换：字符数组、大小写变更、新旧字符替换、去除空格、字典顺序比较

3. map的使用

   <key,value>,根据key,遍历value。