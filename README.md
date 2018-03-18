PHP-to-Golang
=============

If you are a PHP developer this is a guide documenting some of the differences you will find while trying to learn the [Go Programming Language](http://golang.org). Many people have written about some of the conceptual differences, but this document focuses on the actual day-in-out differences and is a work-in-progress.

In PHP you have to check if a value exists before you use it.

```php
if (isset($_POST['id'])) {
    $id = $_POST['id'];
}
```

In Go you just access something and it will return the value (if any) and another variable indicating that it found something there.

```go
value, ok = m[key]
```

Then you do the check (if you care)

```go
if ok {
    fmt.Println(value)
}
```

The reason being that in Go you already specified that the array (or "map") "m" was of a certain type ("int"). So Go can return the "zero" value for that type (a "0") while PHP has no idea what you might be storing in `$_POST` it so it requires you to check for a value.

In fact, building on the fact that Go makes you s-p-e-l-l-o-u-t what types you plan on storing it your variables you can do neat things like increment any zero'ed number value of any array, slice, or map even if the element doesn't exist yet. Take the following code that counts the number of times a word appears inside a string.

```go
string_array := strings.Fields(s)
counts := make(map[string]int)

for _, v := range string_array {
    counts[v]++ // No need to do "count[v] = 0" first
}
```

However, PHP has no idea what you might be storing inside the `$count` array (word => count pairs), so you have to set the count to "0" the first time you try to store the word so you can `$counts[$v]++` from then on.

```php
$string_array = str_split($s)
$counts = array();

foreach ($string_array as $v) {
    if(empty($counts[$v])) { // Not needed in Go since map[string]int default is a "0"
        $counts[$v] = 0;
    }
    $counts++;
}
```

Formatting dates in PHP uses special characters that mean something.

```php
print date("d/m/Y H:ia")
```

In Go you simply write out the date `Monday, January 2nd, 2006 at 15:04:05` (which is Month=1, Day=2, Hour=3, Minute=4, Sec=5, Year=6) and Go figures out (based on the number) which parts of what you wrote should be replaced with the real date. *Very smart*.

```go
fmt.Println(time.Now().Format("2/1/2006 at 3:04pm"))
fmt.Println(time.Now().Format("Mon, Jan 2, 2006 at 3:04pm"))
fmt.Println(time.Now().Format("Monday at 3:04pm"))
// etc...
```

In PHP, you create your classes and all properties and methods at once inside the class.

```php
class Dog {
    public $Name;
    public $Color;

    public function __construct($Name, $Color) {
        $this->Name = $Name;
        $this->Color = $Color;
    }
    public function Call() {
        printf("Come here %s dog, %s", $this->Color, $this->Name)
    }
}

$dog = new Dog('Spot', 'brown');
$dog->Call()
```

In Go, you create your class (called a struct) with all properties and then add in methods later (like you can do in JavaScript).

```go
// define Dog class
type Dog struct {
  Name  string
  Color string
}

// Method for the "Dog" object called "Call"
func (d Dog) Call() {
  fmt.Printf("Come here %s dog, %s", d.Color, d.Name)
}

Spot := Dog{Name: "Spot", Color: "brown"}
Spot.Call()
```