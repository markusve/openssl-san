Testing stuff only, no production keys and certificates!
--------------------------------------------------------

# Create client cert (run commands in this directory, having openssl.cnf):
openssl genrsa -out client/client.key.pem 4096
openssl req -new -key client/client.key.pem -out client/client.csr
# Use x509 command instead of ca command to get SAN extension into certificate!
#openssl ca -config ./openssl.cnf -days 1650 -notext -batch -in client/client.csr -out client/client.cert.pem
openssl x509 -req -extfile <(printf "subjectAltName=DNS:client.example.com") -days 365 -in client/client.csr -out client/client.cert.pem -CA demoCA/certs/cacert.pem -CAkey demoCA/private/cakey.pem -CAcreateserial

# Create server cert: 
As for client, but use server directory!

# You can check the serial number with:
cat demoCA/index.txt
openssl x509 -in client/client.cert.pem -noout -serial

# Start server:
openssl s_server -accept 3000 -CAfile demoCA/certs/cacert.pem -cert server/server.cert.pem -key server/server.key.pem -state

# Run client:
echo FOO | openssl s_client -connect 127.0.0.1:3000 -key client/client.key.pem -cert client/client.cert.pem -CAfile demoCA/certs/cacert.pem -state
