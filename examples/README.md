## How to create simple app

#### 1. clone cookiecutter template
```
git clone git@gitlab.allrisk.cz:python/misac/cookiecutter-new-service.git
```

#### 2. run cookiectutter
cookiecutter create new app structure from template
```
cd cookiecutter-new-service
docker-compose run --rm cookiecutter
```

#### 3. edit app code
go to newly created directory, edit source code

##### 3.1 protocol buffers
in `protos` directory create file `validators/person.proto`
```
mkdir validators/protos/validators
```

`person.proto`
```
syntax = "proto3";

package validators.person;

import "google/api/annotations.proto";

// The validator service definition.
service PersonValidator {
    // validate personal identification number
    rpc ValidatePIN (PINRequest) returns (PINResponse) {
        option (google.api.http) = {
            get: "/v1/validators/person/validatePIN/{pin}"
            body: ""
        };
    }
}

// The request message containing the user's personal identification number.
message PINRequest {
    string pin = 1;
}

// The response message containing the greetings
message PINResponse {
    bool valid = 1;
    string err = 2;
}
```

copy dependencies
```
cp -r ./materials/google/ ./validators/protos/
```

generate python stubs
```
docker-compose -f docker-compose-proto-gen.yml up
sudo chown ${USER}:${USER} ./validators/stubs/ -R
```

##### 3.2 create gRPC service
in `validators/services` create directory `validators` and in this directory create file `person.py`

add service to grpc server

run gRPC server
```
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up --build
```

run gRPC client
```
docker-compose -f docker-compose.yml -f docker-compose-dev.yml exec grpc-server sh -c "python client.py"
```