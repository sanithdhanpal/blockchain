# Part3
------
## Database
There’s no database in our implementation; instead, we create blocks every time we run the program and store them in memory. We cannot reuse a blockchain, we cannot share it with others, thus we need to store it on the disk.so we are using sqlite3 for the database to store the blockchain details in it.
## Persistence
 It creates a new instance of Blockchain and adds the genesis block to it.Open a DB file.Check if there’s a blockchain stored in it.If there’s a blockchain: Create a new Blockchain instance.Set the tip of the Blockchain instance to the last block hash stored in the DB.If there’s no existing blockchain:Create the genesis block.Store in the DB.Save the genesis block’s hash as the last block hash.Create a new Blockchain instance with its tip pointing at the genesis block.




```sh
import hashlib as hasher
import datetime as date
import sys
import sqlite3


conn = sqlite3.connect('db.sqlite')
cur = conn.cursor()

#cur.execute('DROP TABLE IF EXISTS blockchain')
#cur.execute('CREATE TABLE blockchain(block INTEGER PRIMARY KEY, data TEXT, hash TEXT, prevhash TEXT, nonce INTEGER)')
cur.execute('select count(*) from blockchain')
total = cur.fetchone()[0]

nonce = 0
blockchain = []



class Block:
  def __init__(self, index, timestamp, data, previous_hash, nonce):
    self.index = index
    self.timestamp = timestamp
    self.data = data
    self.nonce = nonce  
    self.previous_hash = previous_hash
    self.hash = self.hash_block()
  
  def hash_block(self):
    sha = hasher.sha256()
    sha.update(str(self.index) + 
               str(self.data) + 
               str(self.previous_hash)+
               str(self.timestamp) + 
               hex(nonce))
    return sha.hexdigest()

#creating genesis Block
def create_genesis_block():
# Manually construct a block with
# index zero and arbitrary previous hash
  return Block(0, date.datetime.now(), "Genesis Block", "0", 0) #last parameter is nonce


#adding blocks one-by-one
def next_block(last_block):
  this_index = last_block.index + 1
  this_timestamp = date.datetime.now()
  this_data = "Hey! I'm block " + str(this_index)
  this_hash = last_block.hash
  this_nonce = last_block.nonce
  return Block(this_index, this_timestamp, this_data, this_hash, this_nonce)


# Create the blockchain and add the genesis block
if(total == 0):
  target_bits = 24
  target = 2 ** (256-target_bits)

  nonce = 0
  while nonce < sys.maxint:
    first_block = create_genesis_block()
    if(long(first_block.hash, 16) < target):
      blockchain.append(first_block)
      previous_block = blockchain[0]
      break
    else:
      nonce = nonce + 1


  print("Genesis Block  has been added to the blockchain!")
  print("Previous Hash: 0")
  print("Hash: {}".format(first_block.hash)) 
  print("data: {}\n".format(first_block.data))
  print("nonce value for block #{}".format(nonce))

  cur.execute('INSERT INTO blockchain (data, hash, prevhash, nonce) VALUES (?,?,?,?)', (first_block.data, first_block.hash, first_block.previous_hash, nonce))
  conn.commit()


    #blockchain = [create_genesis_block()]
    #previous_block = blockchain[0]



# How many blocks should we add to the chain
#  after the genesis block
num_of_blocks_to_add = input("how many blocks would you like to add to the chain")


timestamp = date.datetime.now()
cur.execute('SELECT * from blockchain ORDER BY block DESC limit 1')
last_block_index = cur.fetchone()[0]
print(last_block_index)

cur.execute('SELECT * from blockchain ORDER BY block DESC limit 1')
data = cur.fetchone()[1]
print(data)

cur.execute('SELECT * from blockchain ORDER BY block DESC limit 1')
hash = cur.fetchone()[2]
print(hash)

cur.execute('SELECT * from blockchain ORDER BY block DESC limit 1')
previous_hash = cur.fetchone()[3]
print(previous_hash)

cur.execute('SELECT * from blockchain ORDER BY block DESC limit 1')
nonce = cur.fetchone()[4]
print(nonce)




previous_block = Block(last_block_index, timestamp, data, previous_hash, nonce)


# Add blocks to the chain
for i in range(0, num_of_blocks_to_add):
  if(i < num_of_blocks_to_add):
    target_bits = 24
    target = 2 ** (256-target_bits)

    nonce = 0

    #we iterate it till the max int value of python
    while nonce < sys.maxint:
      block_to_add = next_block(previous_block)    

      #proof-of-work is calculated here
      #that is if hash value is less than target value, then we add it to the blockchain 
      #since it satisfied the requirements.
      #that is we require 3 bytes of zeros(000000) 6 zeros
      if(long(block_to_add.hash, 16) < target):
        blockchain.append(block_to_add)
        previous_block = block_to_add
        break
      else:
        nonce = nonce + 1
  

  


    print("Block #{} has been added to the blockchain!".format(block_to_add.index))
    print("Previous Hash: {}".format(block_to_add.previous_hash))
    print("Hash: {}".format(block_to_add.hash)) 
    print("data: {}\n".format(block_to_add.data))
    print("nonce value for block #{}".format(nonce))

    cur.execute('INSERT INTO blockchain (data, hash, prevhash, nonce) VALUES (?,?,?,?)', (block_to_add.data, block_to_add.hash, block_to_add.previous_hash, nonce))
    conn.commit()

  else:
    cur.close()
    break





  











```
