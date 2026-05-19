
sequenceDiagram

    autonumber
    title Test_Git_mermaid - Updated in Git at 19:05
    
    participant P as Client 
    participant WS as Website
    participant GRT as GRT Wrapper
    participant GAME as Game iframe
    participant WS as Operator Frontend
    participant OP as Operator Backend
    participant HW as Hub.Web
        participant HV as Hub Vendor
    participant V as Vendor

Note over P, V: Game Launch
    P->>WS : Open Game
    WS ->> OP:Open Game
    activate OP
    OP->>HW: POST LaunchGame{playerId, gameCode, regulation}
    activate HW
    Note over HW: If the token is sent in requests, it is ignored
    HW->>HW: Generate and store operatorToken
    HW ->> HW: Check Jurisdiction  
        opt Player can't play from the jurisdiction (by IP)
            HW-->>OP: Error 14 
            OP-->>WS: ???
            WS-->>P: ???
        end
        
    HW-->>OP:  LaunchURL, operatorToken 
    deactivate HW
    OP -->> WS: LaunchURL, operatorToken
    WS->>GRT: Initialize GRT
    GRT->>P:  clock, reality check, compliance UI
    WS->>GAME: Create iframe with LaunchURL

Note over P, V: Authentication
    GAME->>V: GET Launch Game  {gameCode, regulation, operatorToken}
    activate V
    V->>HW: POST authenticate {operatorToken}
    activate HW
    HW->>OP: POST authenticate {operatorToken}
    activate OP
    OP-->>HW: {balance}
    deactivate OP
    HW->>HW: Generate and store vendorToken
    HW-->>V: {vendorToken, balance}
    deactivate HW
    V-->>GAME:  game content
    deactivate V


   
