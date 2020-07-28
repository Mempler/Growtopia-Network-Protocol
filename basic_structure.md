# Navigation

- [Navigation](#navigation)
  - [Basic Structure](#basic-structure)
    - [Known Packet Types](#known-packet-types)
  - [Tank Packet](#tank-packet)
    - [Known Tank Types](#known-tank-types)
    - [Tank Packet Header](#tank-packet-header)
    - [Tank Packet Data](#tank-packet-data)
  - [Text Packet](#text-packet)

## Basic Structure

Growtopia's packet structure is quite a complex one but also quite simple for an MMO RPG
Each packet should have at least this packet structure shown bellow.

```cpp
typedef struct {
    uint32_t PacketType;
    const char* PacketData;
} Packet;
```

### Known Packet Types

```cpp
enum E_PacketType {
    Unknown                 = 0x00,
    LoginInformationRequest = 0x01,
    CTextPacket             = 0x02, // Client
    STextPacket             = 0x03, // Server
    TankPacket              = 0x04,
    
    // TODO: figure out what those do:
    GameError               = 0x05,
    ClientLogRequest        = 0x06,
    ClientLogResponse       = 0x07
};
```

## Tank Packet

a Tank packet contains 2 sub structures, the `Tank Packet Header` where all the information about this packet is stored and the `Tank Packet Data` where all it's data is stored.

### Known Tank Types

```cpp
enum E_TankType {
    UpdateState                    = 0x00,
    CallFunction                   = 0x01,
    UpdateStatus                   = 0x02,
    UpdateTileRequest              = 0x03,
    SendWorld                      = 0x04,
    SendTileUpdate                 = 0x05,
    SendMultipleTileUpdates        = 0x06, // Cluster multiple tile updates together
    RequestActiveTile              = 0x07,
    ApplyTileDamage                = 0x08,
    SendInventory                  = 0x09,
    ItemDatabaseRequest            = 0x0A,
    ItemDatabaseResponse           = 0x0B, // TODO: This could be wrong. original was `ITEM_ACTIVATE_OBJECT_REQUEST`
    SendTileTreeState              = 0x0C,
    ModifyInventoryItem            = 0x0D,

    // TODO: check those and give them proper names:
    ITEM_CHANGE_OBJECT             = 0x0E,
    SEND_LOCK                      = 0x0F,
    SEND_ITEM_DATABASE_DATA        = 0x10,
    SEND_PARTICLE_EFFECT           = 0x11,
    SET_ICON_STATE                 = 0x12,
    ITEM_EFFECT                    = 0x13,
    SET_CHARACTER_STATE            = 0x14,
    PING_REPLY                     = 0x15,
    PING_REQUEST                   = 0x16,
    GOT_PUNCHED                    = 0x17,
    APP_CHECK_RESPONSE             = 0x18,
    APP_INTEGRITY_FAIL             = 0x19,
    Disconnect                     = 0x1A,
    BATTLE_JOIN                    = 0x1B,
    BATTLE_EVENT                   = 0x1C,
    UseDoor                        = 0x1D,
    SEND_PARENTAL                  = 0x1E,
    GONE_FISHIN                    = 0x1F,
    STEAM                          = 0x20,
    PET_BATTLE                     = 0x21,
    NPC                            = 0x22,
    SPECIAL                        = 0x23,
    SEND_PARTICLE_EFFECT_V2        = 0x24,
    ARROW_TO_ITEM                  = 0x25,
    TILE_INDEX                     = 0x26,
    SEND_PLAYER_TRIBUTE_DATA       = 0x27,
}
```

### Tank Packet Header

a Tank packet header looks like as followed

```cpp
typedef struct {
    uint32_t TankData; // byte 1 = TankType, byte 2..4 data
    int32_t  PlayerFlags;
    uint32_t pd1; // Padding
    uint32_t CharacterState;
    uint32_t pd2; // Padding
    uint32_t PlantingTree;
    float    PositionX;
    float    PositionY;
    float    SpeedX;
    float    SpeedY;
    uint32_t pd2; // Padding
    float    PunchDirectionX;
    float    PunchDirectionY;
    uint32_t PacketLength; // Packet length of it's Tank Data
} TankPacketHeader;
```

### Tank Packet Data

Tank packet data can vary a lot! an example would be (and also the easiest one) the `Movement` packet, it consists just of the header and nothing more.

```cpp
typedef struct {
    uint32_t TankData         = 0x00 | 0x00 | 0x00 | 0x00; // byte 1 = TankType, byte 2..4 data
    int32_t  PlayerFlags      = TargetId;
    uint32_t pd1              = 0x00; // Padding
    uint32_t CharacterState   = TargetState;
    uint32_t pd2              = 0x00; // Padding
    uint32_t PlantingTree     = 0x00;
    float    PositionX        = 120.382f;
    float    PositionY        = 293.2f;
    float    SpeedX           = 1.0f; // We move to the right
    float    SpeedY           = -1.0f; // We're jumping
    uint32_t pd2              = 0; // Padding
    float    PunchDirectionX  = 0.0f;
    float    PunchDirectionY  = 0.0f;
    uint32_t PacketLength     = 0x00; // Packet length of it's Tank Data
} TankPacketHeader;
```

## Text Packet

a Text packet is one of the more simpler things as text packets can look as followed:

```csv
someKey|someValue
someKey|someValue
```

there is not much to say here.
