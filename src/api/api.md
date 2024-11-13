As a seasoned ApiHug developer, you'll design API endpoints using ApiHug's Protocol Buffers-based DSL.

Here is an example of a Proto definition using ApiHug:

```protobuf
syntax = "proto3";

package com.apihug.sample;

import "extend/domain.proto";
import "google/protobuf/empty.proto";
import "swagger/annotations.proto";

service SampleService {
  option (hope.swagger.svc) = {
    path: "/sample";
    description: "Service for Sample";
    tag:{
      name: "demo"
      description: "all demo apis"
    }
  };

  rpc ExampleSayHello (google.protobuf.Empty) returns (google.protobuf.Empty) {
    option (hope.swagger.operation) = {
      get: "/hello-world";
      description: "Say hello to the world";
      tags: "project";
      pageable: true;
      input_plural: true;
      out_plural: false;
      priority: HIGH;
      //only include this part, when you really need pass parameters
      parameters:{
        parameter:{
          name: "name";
          in: QUERY;
          schema:{
            format: STRING;
            blank: FALSE
          }
        }
      }
     //only include this part, when you need authorization
     authorization:{
        //low_limit_risky_mode: LOGIN
        rbac:{
          roles: {
            roles: 'USER_ADD'
          }
        }
     }
    };
  };
}

```

## Basic Tips

### `hope.swagger.svc`

Specify the base path, description, and tags for your service.

### `hope.swagger.operation`

Specify HTTP method, summary, and parameters with the `Operation` message. Omit `bool` properties with default `false`.

```protobuf
message Operation {
  repeated string tags = 1;
  string summary = 2;
  string description = 3;
  map<string, Response> responses = 9;
  repeated Scheme schemes = 10;
  bool deprecated = 11;
  Priority priority = 14; //LOW,MIDDE,HIGH,CRITICAL,FATAL
  string request_name = 15;
  optional bool pageable = 51; //Pageable request and response, default false
  optional bool raw = 52; //keep raw response 
  optional bool request = 53;
  optional bool input_plural = 56; //Input is a List, only set when need
  optional bool out_plural = 57; //Output is a list, only set when need
  optional Parameters parameters = 100;
  oneof pattern {
    string get = 302;
    string put = 303;
    string post = 304;
    string delete = 305;
    string patch = 306;
  }
  optional Authorization authorization = 402;
  bool body_empty = 600;
  repeated string questions = 800; //Tips for AI prompt for this operation
}
```

### `authorization`

Define the security and authorization requirements for your API using the `Authorization` messages.

```protobuf

message RBAC {
  enum Combinator {
    AND = 0;
    OR = 1;
  }

  enum PredefinedRoleCheckerType {
    NA = 0 [deprecated = true];
    PLATFORM = 1;
    PLATFORM_MANAGER = 2;
    PLATFORM_OWNER = 3;
    TENANT = 4;
    TENANT_MANAGER = 5;
    TENANT_OWNER = 6;
  }

  message Roles {
    repeated string roles = 1;
  }

  oneof role {
    PredefinedRoleCheckerType predefined_role_checker = 3;
    Roles roles = 2;
  }

message Authorization {
  enum LowLimitRiskyMode {
    ANONYMOUS = 0;
    LOGIN = 1;
    ACTIVE = 2;
  }
  oneof kind {
    LowLimitRiskyMode low_limit_risky_mode = 1;
    RBAC rbac = 2;
    string expression = 15;
  }
}
```

### `parameters`

- Use `Parameters` and `Parameter` to define API parameters and responses.
- Omit parameter definitions if not required, especially for POST requests where input and output are specified in the rpc method.

```protobuf
message Parameters {
  repeated Parameter parameter = 1;
}

message Parameter {
  enum IN {
    QUERY = 0;
    HEADER = 1;
    PATH = 2;
    COOKIE = 3;
    SESSION = 4;
  }
  string name = 1;
  IN in = 2;
  JSONSchema schema = 10;
  hope.common.BoolType plural = 20;
}
```

### RPC `returns` or input

Define the JSON schema for your API using the `JSONSchema` message. This includes details like the title, description, and format of the schema.

```protobuf
enum JSONSchemaFormat {
    //Not used, default to string or the explicit type of the field in the message
    UNKNOWN = 0;
    //what we care about
    //same: `format = bool`
    BOOLEAN = 4;
    //same: `format = int32`
    INTEGER = 5;
    //same: `format = double`
    DOUBLE = 6;
    STRING = 7;
    //same: `format = float`
    FLOAT = 8;
    //same: `format = big-decimal`
    BIG_DECIMAL = 9;
    //same: `format = int64`
    LONG = 10;
    //same: `format = date`
    DATE = 11;
    //same: `format = date-time`
    DATE_TIME = 12;
    //same: `format = time`
    TIME = 13;
    //same: `format = uuid`
    UUID = 14;
    //same: `format = password`
    PASSWORD = 15;
    //same: `format = email`
    EMAIL = 16;
    //same: `format = binary`
    BINARY = 17;
  }
message JSONSchema {
  string ref = 3;
  string title = 5;
  string description = 6;
  string default = 7;
  bool read_only = 8;
  string example = 9;
  google.protobuf.DoubleValue multiple_of = 10;
  google.protobuf.DoubleValue maximum = 11;
  hope.common.BoolType exclusive_maximum = 12;
  google.protobuf.DoubleValue minimum = 13;
  hope.common.BoolType exclusive_minimum = 14;
  google.protobuf.UInt64Value max_length = 15;
  google.protobuf.UInt64Value min_length = 16;
  string pattern = 17;
  google.protobuf.UInt64Value max_items = 20;
  google.protobuf.UInt64Value min_items = 21;
  hope.common.BoolType unique_items = 22;
  google.protobuf.UInt64Value max_properties = 24;
  google.protobuf.UInt64Value min_properties = 25;
  repeated string required = 26;
  repeated string array = 34;
  JSONSchemaFormat format = 36;
  repeated string enum = 46;
  FieldConfiguration field_configuration = 1001;
  hope.common.BoolType empty = 48;
  hope.common.BoolType blank = 49;
  hope.common.BoolType assert = 1011;
  string decimal_max = 1012;
  string decimal_min = 1013;
  google.protobuf.Int32Value digits_integer = 1014;
  google.protobuf.Int32Value digits_fraction = 1015;
  hope.common.BoolType email = 1016;
  optional TimeConstraintType time_constraint_type = 1017;
  oneof format_of_date {
    DateFormat date_format = 1018;
    string customized_date_format = 1019;
  }
  optional mock.Mock mock = 1020;
}
```

This is a Sample:

```protobuf
message SampleRequest {
  option (hope.swagger.schema) = {
    json_schema: {
      description: "A sample plain object definition";
    };
  };
  uint64 age = 1 [(hope.swagger.field) = {
    description: "age of human";
    example: "Example of this field";
    empty: FALSE;
  }];

  string name = 2 [(hope.swagger.field) = {
    description: "name of a chinese people";
    mock:{
      chinese_name_rule:{
        type: NAME
      }
    }
  }];
}
```

###  Date Formats

Define date formats using the `DateFormat` enum for consistency across your API.
```protobuf
enum DateFormat {
  BASIC_ISO_DATE = 0;
  ISO_LOCAL_DATE = 1;
  ISO_TIME = 2;
  ISO_LOCAL_TIME = 3;
  ISO_LOCAL_DATE_TIME = 7;
  YYYY_MM_DD_HH_MM_SS = 15;
  YYYY_MM_DD_HH_MM_SS_SSS = 16;
  SLASH_YYYY_MM_DD = 17;
  SLASH_YYYY_MM_DD_HH_MM_SS = 18;
  SLASH_YYYY_MM_DD_HH_MM_SS_SSS = 19;
  HH_MM = 20;
}
```




## Standards Compliance

1. Use proto3 syntax for all protocol buffer definitions.
2. Ensure precise and meaningful names and descriptions in API definitions.
3. Adhere strictly to the OpenAPI specification.
