# Introduction
Blockchain is one of the most revolutionary technologies which is still maturing and which potential is not fully realized yet. In its essence, blockchain is just a distributed database of records. But  it’s public one, i.e. everyone who uses it has a full or partial copy of it. And a new record can be added only with a consent of other keepers of the database.

In blockchain it’s blocks that store valuable information. Besides this, a block contains some technical information, like its version, current timestamp and the hash of the previous block. The blocks are stored in the insertion order and that each block is linked to the previous one.In any blockchain, there must be at least one block, and such block, the first in the chain, is called genesis block.

```sh
import hashlib as hasher
import datetime as date


class Block:
  def __init__(self, index, timestamp, data, previous_hash):
    self.index = index
    self.timestamp = timestamp
    self.data = data
    self.previous_hash = previous_hash
    self.hash = self.hash_block()

  def hash_block(self):
    sha = hasher.sha256()
    sha.update(str(self.index) + 
               str(self.timestamp) + 
               str(self.data) + 
               str(self.previous_hash))
    return sha.hexdigest()


def create_genesis_block():
  
  return Block(0, date.datetime.now(), "Genesis Block", "0")    

def next_block(last_block):
  this_index = last_block.index + 1
  this_timestamp = date.datetime.now()
  this_data = "Hey! I'm block " + str(this_index)
  this_hash = last_block.hash
  return Block(this_index, this_timestamp, this_data, this_hash)  



blockchain = [create_genesis_block()]
previous_block = blockchain[0]


num_of_blocks_to_add = 20


for i in range(0, num_of_blocks_to_add):
  block_to_add = next_block(previous_block)
  blockchain.append(block_to_add)
  previous_block = block_to_add
  
 
  print "previous_hash: {}\n".format(block_to_add.previous_hash)
  print "Hash: {}\n".format(block_to_add.hash)
  print "Data: {}\n".format(block_to_add.data) 

```
