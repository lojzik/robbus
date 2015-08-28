# Packet structure description #

## Packet structure ##
The basic packet structure looks like this:
```
<Head><Address>[Mask]<Payload Length><Payload><Checksum>
```

## Reserved values ##

values 0x00 to 0x03 are reserved. If they occur in the packet body, they are converted to 0x00 prefix and value increased by 4. So i.e. 0x02 changes  to the 0x00 0x06 pair.

## Head byte ##

Head byte indicates packet type. There are three allowed types. **0x01** means **service packet**. It is used for presence check (ping) or node address change.

Regular **data packet** has head byte value **0x02**. This is the regular request and node will respond.

There is another type, **group packet**. It has head value **0x03** and it's a kind of multicast. The nodes shall not respond to it because of collision on the bus.


## Address ##

For regular and service packet, the address is just one byte. It must be in range **0x04** to **0x07f**. In the response packet, the address byte has value from the request increased by 128 (the MSB is set). However we recommend using alphanumerical addresses so you can easily recognize them in the log.

For group packet the address has two bytes - address and mask. The node accepts the packet if following condition passes:
```
(packetMaskask & packetAddressAddress) == (packetMaskask & packetAddressAddress & deviceAddress)
```

The mask value is converted to 0x04-0xff range.

## Payload Length ##
This byte indicates payload length in **original** bytes. So even if there is 0x01 value represented bu 0x00 0x05 pair, it means just 1 in payload length meaning. The payload length is fixed per node and is validated in reference implementation. The value is converted to 0x04-0xff range.

## Payload ##

These are the actual data transfered. They are converted to 0x04-0xff range.

## Checksum ##
The packet has a simple checksum. If you sum all **original** (not converted) bytes from address to checksum (without the head byte) you must get **0x00**. The value is converted to 0x04-0xff range.