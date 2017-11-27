
# A word about platforms

## UI
- single thread for updating the UI
- error if updated from other thread
- do as little as possible on UI thread to keep it responsive
- UI thread crashes -> app crashes

## Threaded servers
- each request processed on one thread
- has a pool of threads
- avoid work outside the request thread as it will consume threads from the pool and kill scalability
- reaponsivenes is guaranteed by having threads in the pool which can be used

## Event-driven servers
- one thread processes requests
- performance is gained by not switching/scheduling threads
- must use asynchrnous operations to be able to scale
- heavy calculations will block the server

## Console
- no running model
- by default every thing is run on different threads
- can be adaped to use one of the other models

