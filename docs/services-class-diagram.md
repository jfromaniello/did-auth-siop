````mermaid
classDiagram


class DidAuthValidationResponse {
    <<interface>>
    signatureValidation: boolean;
    signer: 'VerificationMethod (see_other_diagram)';
    payload: JWTPayload;
}
DidAuthValidationResponse --> JWTPayload


class RPAuthService {
    <<service>>
    verifyAuthResponse(idToken: string, audience: string) Promise(DidAuthValidationResponse)
}
DidAuthValidationResponse <-- RPAuthService


class RPSession {
    <<service>>
    createAccessToken(validation: DidAuthValidationResponse) Promise(AkeResponse)
    verifyAccessToken(accessToken: string) Promise(JWTPayload)
}
RPSession <-- DidAuthValidationResponse
RPSession --> AkeResponse
RPSession --> JWTPayload

class AuthRequestService {
    <<service>>
    createAuthRequest(didAuthRequestCall: DidAuthRequestCall) Promise(uri: string)
    verifyAuthRequest(didAuthJwt: string): Promise(DidAuthRequest)
    createAuthResponse(didAuthResponse: DidAuthResponseCall): Promise(UriResponse);
}
AuthRequestService <-- DidAuthRequestCall
AuthRequestService <-- DidAuthResponseCall
AuthRequestService --> UriResponse

class DidAuthResponseCall {
    <<interface>>
    hexPrivateKey: string;
    did: string;
    redirectUri: string;
    nonce?: string;
    responseMode?: ResponseMode;
    claims?: ResponseClaims;
}
DidAuthResponseCall --> ResponseMode
DidAuthResponseCall --> ResponseClaims

class ResponseClaims {
    <<interface>>
    verified_claims?: string;
    encryption_key?: JsonWebKey;
}

class ResponseMode {
    <<enum>>
}

 class UriResponse {
    <<interface>>
    responseMode?: ResponseMode;
    bodyEncoded?: string;
}
UriResponse --> ResponseMode
UriDidAuth <|-- UriResponse

class UriDidAuth {
    <<interface>>
    urlEncoded: string;
    encoding: UrlEncodingFormat;
}
UriDidAuth --> UrlEncodingFormat

class UrlEncodingFormat {
    <<enum>>
}


class DidAuthRequest {
    <<interface>>
    iss: string;
    scope: Scope;
    response_type: ResponseType;
    client_id: string;
    nonce: string;
    did_doc?: DIDDocument;
    claims?: RequestClaims;
}
DidAuthRequest <|-- JWTPayload

class  JWTPayload {
  iss?: string
  sub?: string
  aud?: string | string[]
  iat?: number
  nbf?: number
  exp?: number
  rexp?: number
  [x: string]: any
}

class DidAuthRequestCall {
    <<interface>>
    redirectUri: string;
    hexPrivateKey: string;
    kid: string;
    issuer: string;
    responseMode?: string;
    responseContext?: string;
    claims?: RequestClaims;
}
DidAuthRequestCall --> RequestClaims

class RequestClaims {
    <<interface>>
    userinfo?: UserInfo (external_openid_userinfo);
    id_token?: IdToken (external_openid_userinfo);
}

class ClientAgent {
    <<service>>
    verifyAuthResponse(response: AkeResponse, nonce: string) Promise(AkeDecrypted);
}
ClientAgent <-- AkeResponse
ClientAgent --> AkeDecrypted

class AkeResponse {
    <<interface>>
    version: 1;
    signed_payload: AkeSigned;
    jws: string;
    did?: string;
}
AkeResponse --> AkeSigned

class AkeSigned {
    <<interface>>
    version: 1;
    encrypted_access_token: string;
    nonce: string;
    kid?: string;
    iat: number;
    iss: string;
}

class  AkeDecrypted {
    <<interface>>
    version: 1;
    access_token: string;
    kid: string;
    nonce: string;
}
````