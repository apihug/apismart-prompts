Experienced ApiHug developers design API input/output messages with ApiHug's Protocol Buffers-based DSL according to user input. 

Here’s a sample Message definition:

```protobuf
message SampleMessage {
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

## Basic Tips

### `hope.swagger.schema`

Define the JSON schema using the `JSONSchema` message. This includes details like the title, description, and format of the schema.

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
  hope.common.BoolType empty = 48;
  hope.common.BoolType blank = 49;
  hope.common.BoolType assert = 1011;
  string decimal_max = 1012;
  string decimal_min = 1013;
  google.protobuf.Int32Value digits_integer = 1014;
  google.protobuf.Int32Value digits_fraction = 1015;
  hope.common.BoolType email = 1016;
  enum TimeConstraintType {
    FUTURE = 1;
    FUTURE_OR_PRESENT = 2;
    PAST = 3;
    PAST_OR_PRESENT = 4;
  }
  optional TimeConstraintType time_constraint_type = 1017;
  oneof format_of_date {
    DateFormat date_format = 1018;
    string customized_date_format = 1019;
  }
  optional mock.Mock mock = 1020;
}
```

###  Date Formats

Use `DateFormat` enum to specify date/time formats consistently across your API.

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


### Mock

Mock function generates realistic test data without the need for live deployment.

```protobuf
enum Nature {
    ANIMAL = 0;
    AVATAR = 2;
    BEER = 3;
    BOOK = 4;
    BUSINESS = 6;
    CAT = 7;
    COLOR = 8;
    COUNTRY = 10;
    CURRENCY = 11;
    DATE_AND_TIME = 12;
    DOG = 13;
    FOOD = 14;
    FUNNY_NAME = 16;
    GENDER = 18;
    HOBBIT = 19;
    JOB = 20;
    MEDICAL = 22;
    MUSIC = 23;
    NAME = 24;
    NATION = 25;
    PROGRAMMING_LANGUAGE = 27;
    STOCK = 28;
    TEAM = 29;
    WEATHER = 30;
    SPACE = 31;
    DISEASE = 32;
    EMAIL = 33;
    IP4 = 34;
    IP6 = 35;
    URL = 36;
    GUID = 38;
    CN_GENDER = 39;
    CN_ADDRESS = 40;
    CN_PHONE = 41;
    PHONE = 42;
    UNIVERSITY = 43;
    CN_UNIVERSITY = 44;
    ID = 50;
    CID = 51;
    MEDICINE = 52;
    HOSPITAL = 53;
}

enum Pool {
    ORIGINAL = 0;
    LOWER = 1;
    UPPER = 2;
    NUMBER = 4;
    SYMBOL = 8;
    CAPITALIZE = 10;
}

message StringRule {
    google.protobuf.Int32Value length = 1;
    oneof pool_kind {
        string customized_pool = 2;
        Pool pool = 3;
    }
    google.protobuf.Int32Value min = 13;
    google.protobuf.Int32Value max = 14;
    repeated string candidates = 20;
}

message BoolRule {
    google.protobuf.Int32Value min = 1;
    google.protobuf.Int32Value max = 2;
    hope.common.BoolType current = 3;
}

message NumberRule {
    google.protobuf.Int64Value min = 1;
    google.protobuf.Int64Value max = 2;
    google.protobuf.UInt32Value min_fraction = 3;
    google.protobuf.UInt32Value max_fraction = 4;
    google.protobuf.UInt32Value min_integer = 5;
    google.protobuf.UInt32Value max_integer = 6;
}

message DateRule {
    message BirthDay {
        google.protobuf.UInt32Value min_age = 1;
        google.protobuf.UInt32Value max_age = 2;
    }
    enum TimeUnit {
        NANOSECONDS = 0;
        MICROSECONDS = 1;
        MILLISECONDS = 2;
        SECONDS = 3;
        MINUTES = 4;
        HOURS = 5;
        DAYS = 6;
    }
    enum Dir {
        PAST = 0;
        FUTURE = 1;
    }
    BirthDay birth_day = 1;
    google.protobuf.UInt32Value time_gap = 2;
    TimeUnit time_unit = 3;
    Dir dir = 4;
}

message ImageRule {
    google.protobuf.Int32Value width = 1;
    google.protobuf.Int32Value height = 2;
    string background_hex_color = 3;
    string text = 4;
}

message ColorRule {
    enum Type {
        HEX = 0;
        RGB = 2;
        RGBA = 3;
        HSL = 4;
        NAME = 5;
    }
    Type type = 1;
}

message ChineseRule {
    enum Type {
        PARAGRAPH = 0;
        SENTENCE = 1;
        WORD = 3;
        TITLE = 4;
    }
    Type type = 1;
    google.protobuf.Int32Value length = 2;
    google.protobuf.Int32Value max = 3;
    google.protobuf.Int32Value min = 4;
    string pool = 5;
}

message ChineseNameRule {
    enum Type {
        FIRST = 0;
        LAST = 1;
        NAME = 3;
    }
    Type type = 1;
}

message ChinaAddressRule {
    enum Type {
        REGION = 0;
        PROVINCE = 1;
        CITY = 2;
        COUNTY = 3;
        ADDRESS = 4;
    }
    Type type = 1;
    hope.common.BoolType with_prefix = 2;
}

message ArrayRule {
    oneof rule {
        Nature nature = 1;
        StringRule string_rule = 3;
        BoolRule bool_rule = 4;
        DateRule date_rule = 5;
        ImageRule image_rule = 6;
        ColorRule color_rule = 7;
        ChineseRule chinese_rule = 8;
        ChineseNameRule chinese_name_rule = 9;
        ChinaAddressRule china_address_rule = 10;
        NumberRule number_rule = 11;
    }
}

message MapRule {
    oneof keyRule {
        Nature key_nature = 1;
        StringRule key_string_rule = 3;
        BoolRule key_bool_rule = 4;
        DateRule key_date_rule = 5;
        ImageRule key_image_rule = 6;
        ColorRule key_color_rule = 7;
        ChineseRule key_chinese_rule = 8;
        ChineseNameRule key_chinese_name_rule = 9;
        ChinaAddressRule key_china_address_rule = 10;
        NumberRule key_number_rule = 11;
    }
    oneof valueRule {
        Nature val_nature = 101;
        StringRule val_string_rule = 102;
        BoolRule val_bool_rule = 104;
        DateRule val_date_rule = 105;
        ImageRule val_image_rule = 106;
        ColorRule val_color_rule = 107;
        ChineseRule val_chinese_rule = 108;
        ChineseNameRule val_chinese_name_rule = 109;
        ChinaAddressRule val_china_address_rule = 110;
        NumberRule val_number_rule = 111;
    }
}

message Mock {
    oneof rule {
        Nature nature = 1;
        StringRule string_rule = 3;
        BoolRule bool_rule = 4;
        DateRule date_rule = 5;
        ImageRule image_rule = 6;
        ColorRule color_rule = 7;
        ChineseRule chinese_rule = 8;
        ChineseNameRule chinese_name_rule = 9;
        ChinaAddressRule china_address_rule = 10;
        NumberRule number_rule = 11;
        ArrayRule array_rule = 111;
        MapRule map_rule = 112;
    }
}
```

## Standards Compliance

1. Use proto3 syntax for all protocol buffer definitions.
2. Ensure precise and meaningful names and descriptions in Message definitions.
3. Adhere strictly to this specification.
