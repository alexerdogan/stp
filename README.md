### Stream processor for live streaming

### QuickStart

Download a corresponding one from precompiled [Releases](https://github.com/alexerdogan/stp/releases).

### Install from source

```
$ export GO111MODULE=on
$ go get -u github.com/alexerdogan/stp/...
```

All precompiled releases are generated from `build-release.sh` script.

#### Usage

```
➜  ~ ./rx_proxy -h
NAME:
   rx_proxy - client

USAGE:
   rx_proxy [global options] command [command options] [arguments...]

VERSION:
   20190924

COMMANDS:
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --serveraddr value, -s value     server address 
   --localaddr value, -l value      local address 
   --key value                      pre-shared secret between client and server 
   --crypt value                    aes, aes-128, aes-192, salsa20, blowfish, twofish, cast5, 3des, tea, xtea, xor, sm4, none (default: "aes")
   --datashard value, --ds value    set reed-solomon erasure coding - datashard (default: 10)
   --parityshard value, --ps value  set reed-solomon erasure coding - parityshard (default: 3)
   --de-jitterbuf value             set de-jitter buffer size in milisecond
   --keepalive value                seconds between heartbeats (default: 10)
   --snmplog value                  collect snmp to file, aware of timeformat in golang, like: ./snmp-20190924.log
   --snmpperiod value               snmp collect period, in seconds (default: 60)
   --log value                      specify a log file to output, default goes to stderr
   --quiet                          to suppress the 'stream open/close' messages
   -c value                         config from json file, which will override the command from shell
   --help, -h                       show help
   --version, -v                    print the version
   
➜  ~ ./tx_proxy -h
NAME:
   tx_proxy - server

USAGE:
   tx_proxy [global options] command [command options] [arguments...]

VERSION:
   20190924

COMMANDS:
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --listen input value, -l value   local input address 
   --server value, -s value         server address
   --key value                      pre-shared secret between client and server 
   --crypt value                    aes, aes-128, aes-192, salsa20, blowfish, twofish, cast5, 3des, tea, xtea, xor, sm4, none (default: "aes")
   --datashard value, --ds value    set reed-solomon erasure coding - datashard (default: 10)
   --parityshard value, --ps value  set reed-solomon erasure coding - parityshard (default: 3)
   --keepalive value                seconds between heartbeats (default: 10)
   --snmplog value                  collect snmp to file, aware of timeformat in golang, like: ./snmp-20190924.log
   --snmpperiod value               snmp collect period, in seconds (default: 60)
   --pprof                          start profiling server on :7070
   --log value                      specify a log file to output, default goes to stderr
   --quiet                          to suppress the 'stream open/close' messages
   -c value                         config from json file, which will override the command from shell
   --help, -h                       show help
   --version, -v                    print the version
```

#### Forward Error Correction

In coding theory, the [Reed–Solomon code](https://en.wikipedia.org/wiki/Reed%E2%80%93Solomon_error_correction) belongs to the class of non-binary cyclic error-correcting codes. The Reed–Solomon code is based on univariate polynomials over finite fields.

It is able to detect and correct multiple symbol errors. By adding t check symbols to the data, a Reed–Solomon code can detect any combination of up to t erroneous symbols, or correct up to ⌊t/2⌋ symbols. As an erasure code, it can correct up to t known erasures, or it can detect and correct combinations of errors and erasures. Furthermore, Reed–Solomon codes are suitable as multiple-burst bit-error correcting codes, since a sequence of b + 1 consecutive bit errors can affect at most two symbols of size b. The choice of t is up to the designer of the code, and may be selected within wide limits.

Setting parameters of RS-Code with ```-datashard m -parityshard n``` on **BOTH** rx_proxy & tx_proxy **MUST** be **IDENTICAL**.

#### Cryptanalysis

xmit-proxy is shipped with builtin packet encryption powered by various block encryption algorithms and works in [Cipher Feedback Mode](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_Feedback_(CFB)), for each packet to be sent, the encryption process will start from encrypting a [nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) from the [system entropy](https://en.wikipedia.org/wiki//dev/random), so encryption to same plaintexts never leads to a same ciphertexts thereafter.

Important: 
1. `-crypt` and `-key` must be the same on both rx_proxy & tx_proxy.

### Identical Parmeters

The parameters below **MUST** be **IDENTICAL** on **BOTH** side:

1. -key
1. -crypt
1. -datashard
1. -parityshard

### References

1. https://github.com/skywind3000/kcp -- KCP - A Fast and Reliable ARQ Protocol.
1. https://github.com/xtaci/kcp-go/ -- A Production-Grade Reliable-UDP Library for golang
1. https://github.com/klauspost/reedsolomon -- Reed-Solomon Erasure Coding in Go.
1. https://en.wikipedia.org/wiki/Differentiated_services -- DSCP.
1. https://www.backblaze.com/blog/reed-solomon/ -- Reed-Solomon Explained.
1. http://www.qualcomm.cn/products/raptorq -- RaptorQ Forward Error Correction Scheme for Object Delivery.
1. https://en.wikipedia.org/wiki/PBKDF2 -- Key stretching.
1. http://blog.appcanary.com/2016/encrypt-or-compress.html -- Should you encrypt or compress first?
1. https://tools.ietf.org/html/rfc6937 -- Proportional Rate Reduction for TCP.
1. https://tools.ietf.org/html/rfc5827 -- Early Retransmit for TCP and Stream Control Transmission Protocol (SCTP).
1. http://http2.github.io/ -- What is HTTP/2?
1. http://www.lartc.org/ -- Linux Advanced Routing & Traffic Control
1. https://en.wikipedia.org/wiki/Noisy-channel_coding_theorem -- Noisy channel coding theorem

