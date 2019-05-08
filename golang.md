+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```