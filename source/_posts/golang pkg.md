---

---

# fmt

- `%v` vs `%#v`

  ```golang
  type Example struct {
      Name  string
      Value int
  }
  
  func main() {
      ex := Example{Name: "test", Value: 42}
  
      // Using %v
      fmt.Printf("Default format: %v\n", ex)
      // Output: Default format: {test 42}
  
      // Using %#v
      fmt.Printf("Go syntax representation: %#v\n", ex)
      // Output: Go syntax representation: main.Example{Name:"test", Value:42}
  }
  ```

  