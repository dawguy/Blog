# Crazy idea

To simplify calling this, we can place all the information (code, index size, bit size) etc in an edn at the start of the index as metadata.

That way instead of 

(open "/index/path" { :a {:b 1 :c 2} :b {:d 3 :e 4} })

we can 

(open "/index/path") and as long as the code stored in the metadata exists at the path specified it will work.

Crazy way to make this easier.

-


