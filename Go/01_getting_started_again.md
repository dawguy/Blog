# Getting Started

Using these two books to get started. Last time I did gophercises which worked well, but a book focused on web development sounds more efficient.

Currently on chapter 3 and finding it quite good. Its like consolidating my year of Clojure usage into something that's ~10 hours long.

https://lets-go.alexedwards.net/
https://lets-go-further.alexedwards.net/


# Random Neat Thing I found -- Iota

https://www.practical-go-lessons.com/chap-27-enum-iota-and-bitmask#iota

Essentially during a const, iota allows you to use successive untyped integer constants.

The below two are identical

A = 0, B = 1, C = 2. You can also bitshift by an iota to make flags very easy to check/compare in a single int.

```go
const (
    A = iota
    B = iota
    C = iota
    )
```

```go
const (
    A = iota
    B
    C
    )
```

Flag example from stdlib log.go package

```go
const (
	Ldate         = 1 << iota     // the date in the local time zone: 2009/01/23
	Ltime                         // the time in the local time zone: 01:23:23
	Lmicroseconds                 // microsecond resolution: 01:23:23.123123.  assumes Ltime.
	Llongfile                     // full file name and line number: /a/b/c/d.go:23
	Lshortfile                    // final file name element and line number: d.go:23. overrides Llongfile
	LUTC                          // if Ldate or Ltime is set, use UTC rather than the local time zone
	Lmsgprefix                    // move the "prefix" from the beginning of the line to before the message
	LstdFlags     = Ldate | Ltime // initial values for the standard logger
)
```

# Networking

Seems like it essentially goes to this. Creates socket file descriptor. 

tcpsock_posix.go

```go
func (ln *TCPListener) accept() (*TCPConn, error) {
	fd, err := ln.fd.accept()
	if err != nil {
		return nil, err
	}
	return newTCPConn(fd, ln.lc.KeepAlive, nil), nil
}

func (fd *netFD) accept() (netfd *netFD, err error) {...}

```

https://man7.org/linux/man-pages/man2/accept.2.html

# Pointer Copying (Shallow)

```go
// Taken from server.go see StripPrefix
func example(p *StructP) *StructP {
    p2 := new(StructP)
    *p2 = *p
    return p2
}
```

# Postgres Specific

To implement an interval you can use `$1::interval` format.

```go
(now() AT TIME ZONE 'utc' - $1::interval)
```

Also useful is the returning type.

https://stackoverflow.com/questions/33382981/go-how-to-get-last-insert-id-on-postgresql-with-namedexec

```go
func (m *SnippetModel) Insert(title string, content string, expires int) (int, error) {
	stmt := `INSERT INTO snippets (title, content, created, expires) VALUES ($1, $2, now(), now() + $3::interval) RETURNING id`

	// https://stackoverflow.com/questions/33382981/go-how-to-get-last-insert-id-on-postgresql-with-namedexec
	lastInsertId := 0
	err := m.DB.QueryRow(stmt, title, content, strconv.Itoa(expires)+" DAYS").Scan(&lastInsertId)
	if err != nil {
		return 0, err
	}

	return lastInsertId, nil
}
```
