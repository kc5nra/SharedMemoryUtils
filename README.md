#SharedMemory Utilities
A collection of small shared memory utilities based on boost::interprocess.
http://www.boost.org/doc/libs/1_55_0/doc/html/interprocess.html

####Server/Clients Messages System
Initialize the server like this:
```c++
sm::MessengerRef mMessages;
mMessages = sm::Messenger::createServer( "SharedMemoryName" );
```

And as many clients as you want:
```c++
sm::MessengerRef mMessages;
mMessages = sm::Messenger::createClient( "SharedMemoryName" );
```
    
You can send and receive messages from both end. The only difference between server and clients is that the server take care of creating and destroying the shared memory segment.
```c++
sm::Message m = mMessages->createMessage( "Test" );
m.addIntArg( 123456 );
m.addFloatArg( 0.987654f );
m.addStringArg( "Hello Shared Memory!" );

mMessages->sendMessage( m );
```
```c++
if( mMessages->hasMessageWaiting() ){
    sm::Message m = mMessages->getFrontMessage();
    cout << m.getAddress() << endl;
    for( int i = 0; i < m.getNumArgs(); i++ ){
        cout << '\t' << m.getArgAsString( i ) << endl;
    }
}
```
####Smart Pointers
Create your shared_ptr like this:
```c++
sm::shared_memory_object::remove( "SharedMemory" );
    
sm::managed_shared_memory sharedMemory  = sm::managed_shared_memory( sm::itp::create_only, "SharedMemory", sizeof(Object) );
sm::shared_ptr<Object> sharedObject     = sm::make_shared( sharedMemory.construct<Object>("SharedObject")(), sharedMemory );
```
And find them on the other side like this:
```c++
sm::managed_shared_memory sharedMemory  = sm::managed_shared_memory( sm::itp::open_only, "SharedMemory" );
sm::shared_ptr<Object> sharedObject     = sm::make_shared( sharedMemory.find<Object>("SharedObject").first, sharedMemory );
```
