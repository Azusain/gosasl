# Go SASL library

gosasl is a library for different SASL mechanism. Currently GSSAPI, PLAIN and ANONYMOUS are implemented. 
Support for other mechanisms may be added in the future. Only GSSAPI supports a QOP higher than auth.

## Example Usage

```go
    mechanism, err := NewGSSAPIMechanism("hive")
	if err != nil {
		log.Fatal(err)
    }    
    conn = getConnection("somehost")
    client := NewSaslClientWithMechanism("somehost", mechanism)
    response, err := client.Start()
    if err != nil {
		log.Fatal(err)
    }
    conn.sendResponse(response)

    for true {
        status, challenge = conn.getChallenge()
        if status == COMPLETE {
            break
        } else if status == OK {
            response = client.Step(challenge)
            conn.sendResponse(response)
        } else {
            log.Fatal("Failed to establish connection")
        }
    }
    if !client.Complete() {
        log.Fatal("SASL negotiation did not complete")
    }

    // begin normal communication
    encoded := conn.fetchData()
    decoded := client.Decode(encoded)
    response = processData(decoded)
    conn.sendData(client.Encoded(response))
```


This library is inspired by [pure-sasl](https://github.com/thobbs/pure-sasl)
