[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Twitter Follow](https://img.shields.io/twitter/follow/strimziio.svg?style=social&label=Follow&style=for-the-badge)](https://twitter.com/strimziio)


# Proxy-Based Kafka Per-Topic Encryption


The goal of this project, currently under development, is to provide topic-level encryption-at-rest for [Apache Kafka](https://kafka.apache.org/). 

Although Kafka provides multiple authentication methods and encrypted communication over [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), it does not encrypt data at rest. Yet Kafka is increasingly used as a store of data, not just as a means of transferring data from one location to another. This means that Kafka must conform to the same security and compliance requirements as conventional data storage systems such as relational databases. To compensate, Kafka service providers often use disk or file system encryption. This approach provides a degree of  risk mitigation but also has well-known shortcomings, most notably the ability of anyone with appropriate file system permissions, such as system administrators, to read data. Security best practices and compliance standards therefore explicitly call for application- or database-level encryption to protect sensitive information against such exposures. Further, there should be a separation of duty between key management and system administration.

This project aims to deliver topic encryption by intercepting the message stream between client and broker in a proxy. The architecture has the advantage that neither broker nor client need to be modified in order to provide encryption at rest. Incoming data messages ([Produce requests](https://cwiki.apache.org/confluence/display/KAFKA/A%2BGuide%2BTo%2BThe%2BKafka%2BProtocol#AGuideToTheKafkaProtocol-ProduceRequest)) are inspected to determine whether their payload should be encrypted according to a policy. If so, the data portions are encrypted and the modified message is forwarded to the broker. The topic data is then stored by the broker in encrypted form. In the reverse direction, encrypted responses (i.e., [responses to Fetch requests](https://cwiki.apache.org/confluence/display/KAFKA/A%2BGuide%2BTo%2BThe%2BKafka%2BProtocol#AGuideToTheKafkaProtocol-FetchResponse)) are decrypted prior to being sent to clients.

The diagram below depicts the main components of the proposal, illustrating clients sending and receiving plaintext messages while the proxy exchanges ciphertext messages with the broker:

![arch](kafkaenc-overview.png)

One core component, the _Encryption Module_, provides the encryption functionality. A second core component, the _Proxy_, intercepts Kafka connections and delegates message processing to the Encryption Module.

Topic can be encrypted by different keys, allowing brokers to store a mix of encrypted and unencrypted data, with data owners managing the keys to their topics. Keys will be stored in an external key management system with access policies and logging.

In the coming weeks we will be providing the specification for the core components along with a roadmap. We look forward to engaging with the Community in developing this exciting extension to Strimzi and Kafka!

P.S. The original [Strimzi proposal #17](https://github.com/strimzi/proposals/blob/master/017-kafka-topic-encryption.md) provides additional background.


