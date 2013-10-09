Things we have learned and common mistakes - todo

TODO(willchan): Copy in advice from SPDY Best Practices doc
TODO(willchan): Note the common bug with allocating stream ids in monotonically increasing order, and how people often screw it up due to priority queues. Can't commit to a stream id when generating a frame and inserting into a priority queue.