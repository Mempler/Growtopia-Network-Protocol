# Navigation
- [Navigation](#navigation)
- [Variant Function](#variant-function)
  - [Variant](#variant)
    - [Variant Types](#variant-types)
    - [Variant Structure](#variant-structure)
    - [Variant List Structure](#variant-list-structure)


# Variant Function

a Variant function is a call towards a [ProtonSDK](https://github.com/SethRobinson/proton.git) function which is declared in Growtopia's protocol, each variant call is a so called *[VariantList](https://github.com/SethRobinson/proton/blob/master/shared/util/Variant.h#L440)* which consists of multiple Variants

## Variant

a [Variant](https://github.com/SethRobinson/proton/blob/master/shared/util/Variant.h) is a "Data store" which holds 1 value and 1 type, you can think of it as an inefficient variable but is required for a [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system)

### Variant Types

```cpp
enum E_VariantType {
    T_NONE       = 0x00,
    T_FLOAT      = 0x01,
    T_STRING     = 0x02,
    T_VECTOR2    = 0x03,
    T_VECTOR3    = 0x04,
    T_UINT32     = 0x05,
    T_ENTITY     = 0x06,
    T_COMPONENT  = 0x07,
    T_RECT       = 0x08,
    T_INT32      = 0x09
}
```

### Variant Structure

```cpp
typedef struct {
    float x;
    float y;
} Vector3;

typedef struct {
    float x;
    float y;
    float z;
} Vector3;

typedef struct {
    E_VariantType type; // Variant Type
	void*         data; // Variant Data
} Variant;

int32_t PackVariant(Variant* var, const char** data) {
    int32_t data_size = 1;

    switch (var.type) {
        case T_NONE:
            // Allocate 1 byte for it's type
            *data = malloc(1);
            break; 

        case T_INT32:  // All of those have a size of 4 so we can
        case T_UINT32: // stack them together!
        case T_FLOAT:
            // Allocate 1 byte for it's type and 4 for it's value (8)
            *data = malloc(5);

            // Copy our Variant's data into data*
            memcpy(*data+1, var.data, 4);
            data_size += 4;
            break;
        case T_STRING:
            uint32_t str_len = strlen((const char*) var.data) + 1;
            // Allocate data based on it's size + 1 for it's type
            // and 4 for it's size
            *data = malloc(str_len + 1 + 4);

            // Copy our str_len into data
            memcpy(*data+1, str_len, 4);
            // Copy it's data
            memcpy(*data+4, var.data, str_len);

            data_size += str_len + 5;
            break;
        case T_VECTOR2:
            // Same as float just for Vector2
            *data = malloc(9);

            memcpy(*data+1, &((Vector2*) var.data).x, 4); // X
            memcpy(*data+1, &((Vector2*) var.data).y, 4); // Y

            data_size += 8;
            break;
        case T_VECTOR3:
            // Same as float just for Vector3
            *data = malloc(13);

            memcpy(*data+1, &((Vector3*) var.data).x, 4); // X
            memcpy(*data+1, &((Vector3*) var.data).y, 4); // Y
            memcpy(*data+1, &((Vector3*) var.data).z, 4); // Z

            data_size += 12;
            break;
        case T_ENTITY:
            // TODO: implement
            break;
        case T_COMPONENT:
            // TODO: implement
            break;
        case T_RECT:
            // TODO: implement
            break;
    
    }

    // Copy it's type into data* which has a size of 1
    memcpy(data, &var.type, 1);

    return data;
}
```

### Variant List Structure
a Variant list is a collection of Variants

```cpp
typedef struct {
    // 16 is the maximum amount of variants we can have in one list
    Variant* variants[16]; 
} VariantList;

// NOTE: this code is for demonstration and prob wont compile
// TODO: fix
uint32_t PackVariantList(VariantList* var_list, const char** data) {
    // I wont go into much detail, it's basically the same as above
    
    data = malloc(1);
    int32_t written_size = 1;

    for (char i = 0; i < 15; i++) {
        Variant* var = var_list.Variant;
        if (var == nullptr_t) // Check if var is null
            break;

        data = realloc(data, written_size+128); // add a tiny buffer

        // Write it's index
        memcpy(data+written_size, &i, 1);
        written_size += 1;
        // Pack it's data

        const char* var_data;
        uint32_t var_size = PackVariant(var, &var_data);

        memcpy(data+written_size, var_data, var_size);
        written_size += var_size;

        free(var_data);
        var_data = nullptr;
    }

    data = realloc(data, written_size); // remove that tiny buffer

    return written_size;
}

```
