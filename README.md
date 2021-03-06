
# mutex
    import "github.com/juju/mutex"

package mutex provides a named machine level mutex shareable between processes.
[![GoDoc](https://godoc.org/github.com/juju/mutex?status.svg)](https://godoc.org/github.com/juju/mutex)

Mutexes have names, and the a mutex with the same name can only be locked by one
instance of the mutex at a time, even across process boundaries.

If a process dies while the mutex is held, the mutex is automatically released.

The linux implementation uses abstract domain sockets, windows uses a named
semaphore, and other platforms use flock on a temp file.





## Variables
``` go
var (
    ErrTimeout   = errors.New("timeout acquiring mutex")
    ErrCancelled = errors.New("cancelled acquiring mutex")
)
```


## type Clock
``` go
type Clock interface {
    // After waits for the duration to elapse and then sends the
    // current time on the returned channel.
    After(time.Duration) <-chan time.Time
}
```
Clock provides an interface for dealing with clocks.











## type Releaser
``` go
type Releaser interface {
    // Release releases the mutex. Release may be called multiple times, but
    // only the first call will release this instance of the mutex. Release is
    // unable to release the mutex successfully it will call panic to forcibly
    // release the mutex.
    Release()
}
```
Releaser defines the Release method that is the only thing that can be done
to a acquired mutex.









### func Acquire
``` go
func Acquire(spec Spec) (Releaser, error)
```
Acquire will attempt to acquire the named mutex. If the Timout value is
hit, ErrTimeout is returned. If the Cancel channel is signalled,
ErrCancelled is returned.




## type Spec
``` go
type Spec struct {
    // Name is required, and must start with a letter and contain at most
    // 40 letters, numbers or dashes.
    Name string
    // Clock must be provided and is exposed for testing purposes.
    Clock Clock
    // Delay defines how often to check for lock acquisition. The implementation
    // calls all fail fast, so acquisition requires polling.
    Delay time.Duration
    // Timeout allows the caller to specify how long to wait.
    Timeout time.Duration
    // Cancel if signalled will cause the Acquire method to return with ErrCancelled.
    Cancel <-chan struct{}
}
```
Spec defines the name of the mutex and behaviour of the Acquire function.











### func (\*Spec) Validate
``` go
func (s *Spec) Validate() error
```
Validate checks the attributes of Spec for validity.









- - -
Generated by [godoc2md](http://godoc.org/github.com/davecheney/godoc2md)