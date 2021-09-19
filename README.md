```go
// Generics definitions. Keyword interface also ok.
generic MyGen(It, *St, ...) {
    // type assertions
    It.(type) int, MyInt     // only one type no need
    
    // interface assertions
    St.(interface) io.Reader // one or more
    
    // method assertions
     (St) Get() It // value receiver
    (*St) Set(It)  // pointer receiver
    
    // generic assertions
    YourGen(St, It)

    // kind assertions, lower reflect.Kind
    //St.(kind)   struct  // can by inferred
    St.F.(kind) int, uint // struct St's field F's kind
    
    // operator assertions
    It.(operator) ==
    It.(operator) +, Add // method precedence
     (It) Add( It)  It   // value method
    (*It) Add(*It) *It   // pointer method
   
    // Because no operator overloading, type and kind assertions should be enough;
    // or reflect package declare operator name and the corresponding method prototype.
}

// Anonymous generics. No assertions.
generic(...) type ...
generic(...) func ...

// Anonymous generics. Inline assertions.
generic(...)
    assertions...
type ...
generic(...)
    assertions...
func ...

// Named generics.
generic Name type ...
generic Name func ...
generic Name(...) type ... // alias parameters
generic Name(...) func ... // alias parameters

// Named generics. Keyword can be omitted.
Name type ...
Name func ...
Name(...) type ... // alias parameters
Name(...) func ... // alias parameters

// Keep parameters simple and stupid.

//// Constraints or nests so ugly.
//generic(G1(C), G2(X, R)) type ...
//generic(C G1, X, R G2) func (...) M(...) (...)

// Always flat.
generic G(C, X, R) {
    G1(C)
    G2(X, R)
    G3(C, R)
}

G type ...
G func ...

// Generics all in conversions, highest precedence.
generic G(I, S) {
    I.(kind) int, uint
    S.(type) string, []byte
}

G type T struct{
    a []I
    m map[I]S
    p *(I, S)T // generic type conversion
}

G func Func(a []I, m map[I]S) (v (I, S)T) {
    v.a = a
    v.m = m
    v.p = &v
    return
}

G func (v (I, S)T) Method() (n I) {
    for _, i := range a {
        if _, ok := m[i]; ok {
            n++
        }
    }
    return
}

// Alias generic type conversion.
G type GT = (I, S)T

G type T struct{
    a []I
    m map[I]S
    p *GT
}

G func Func(a []I, m map[I]S) (v GT) {
    v.a = a
    v.m = m
    v.p = &v
    return
}

G func (v GT) Method() (n I) {
    for _, i := range a {
        if _, ok := m[i]; ok {
            n++
        }
    }
    return
}

func main() {
    a := []int{1, 2, 3}
    m := map[int]string{1: "a", 2: "b"}
    
    type t (int, string)T  // generic type converted to concrete type
    f := (int, string)Func // generic func converted to concrete func
    
    // These function calls are equivalent.
    v := f(a, m)
    v = (int, ``)Func(a, m)
    v = Func(a, m) // can by inferred
    
    v.(type) == t  // true
    
    // These invocations are equivalent.
    n := v.Method()
    n = t.Method(v)
}

generic Addable(T) {
    T.(operator) +, Add
    (T) Add(T) T
}

Addable func Sum(a ...T) (x T) {
    for _, v := range a {
        x += v
        // or
        x = x.Add(v)
    }
    return
}

generic Graph(Node, Edge) {
    (Node) Edges() []Edge
    (Edge) Nodes() []Node
}

generic(T) type List struct {
    elem T
    next *(T)List
}
```

*CHEN Xianren*\
*2021/9/19*
