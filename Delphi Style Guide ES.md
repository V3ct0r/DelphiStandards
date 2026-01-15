<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Traducir al español

El archivo completo ya está en inglés; a continuación está su traducción al español en formato Markdown.

***

# Guía de Estilo Delphi (ES)

> Versión: 2.1
> Autor: Olaf Monien
> Actualizado: 2025-10-08

> Nota: Esta guía de estilo se mantiene en alemán e inglés; esta es una traducción no oficial al español para uso interno. Mantén los documentos originales sincronizados al hacer cambios.[^1]

Esta guía define convenciones de formato y nomenclatura para proyectos modernos en Delphi.  Su objetivo es mejorar la legibilidad, mantenibilidad y consistencia del equipo.[^1]

***

## Inicio rápido - Reglas esenciales

¿Nuevo en el proyecto? Aquí están las reglas esenciales:[^1]

### **Nombres**

```pascal
// Variables
var
  LCustomer: TCustomer;        // Local: prefijo L

type
  TMyClass = class
  private
    FName: string;             // Field/campo: prefijo F
  end;

// Parámetros
procedure DoSomething(const AValue: string);  // Parámetro: prefijo A

// Contadores de bucle - ¡excepción!
for var i := 0 to 10 do       // minúscula, sin prefijo

// Constantes
const
  cMaxRetries = 3;             // Técnica: prefijo c
  scErrorMessage = 'Error';    // Cadena: prefijo sc
```


### **Tipos**

```pascal
type
  TCustomer = class end;           // Clase: prefijo T
  ILogger = interface end;         // Interfaz: prefijo I
  TPoint = record end;             // Record: prefijo T, ¡SIN prefijo F en campos!
  TFileUtils = class sealed end;   // Clase de utilidades: sealed
  TStringHelper = record helper for string end;  // Helper: solo para helpers reales
```


### **Manejo de errores**

```pascal
// Liberar recursos
LObject := TObject.Create;
try
  // Usar el objeto
finally
  FreeAndNil(LObject);  // Siempre FreeAndNil en lugar de .Free
end;

// Múltiples objetos
LQuery := nil;
LList := nil;
try
  LQuery := TFDQuery.Create(nil);
  LList := TList<string>.Create;
finally
  FreeAndNil(LQuery);
  FreeAndNil(LList);
end;
```


### **Formato**

- Sangría de **2 espacios**.[^1]
- **120 caracteres** máximo por línea.[^1]
- `begin..end` siempre en líneas separadas.[^1]
- Prefiere variables inline (a partir de Delphi 10.3+).[^1]


### **Colecciones**

```pascal
// Tamaño fijo → TArray<T>
function GetNames: TArray<string>;

// Lista dinámica → TList<T>
var LNumbers: TList<Integer>;

// Objetos con ownership → TObjectList<T>
var LCustomers: TObjectList<TCustomer>;
```


### **Nombres de unidades (jerarquía de namespaces)**

```pascal
// Formularios terminan en .Form.pas
unit Main.Form;                    // TFormMain / FormMain
unit Customer.Details.Form;        // TFormCustomerDetails / FormCustomerDetails

// Data modules terminan en .DM.pas
unit Main.DM;                      // TDMMain / DMMain
unit Customer.Details.DM;          // TDMCustomerDetails / DMCustomerDetails
```


### **Documentación**

```pascal
/// <summary>
/// Calcula la suma de dos números
/// </summary>
function Add(const AValue1, AValue2: Integer): Integer;
```

**→ Ver la documentación completa más abajo para más detalles.**[^1]

***

## Tabla de contenidos

- [1. Formato](#1-formato)
- [2. Convenciones de nombres](#2-convenciones-de-nombres)
- [3. Estructura de unidades](#3-estructura-de-unidades)
- [4. Estilo de código](#4-estilo-de-c%C3%B3digo)
- [5. Propiedades y getters/setters](#5-propiedades-y-getterssetters)
- [6. Eventos](#6-eventos)
- [7. Misceláneo](#7-miscel%C3%A1neo)
- [8. Funciones modernas de Delphi](#8-funciones-modernas-de-delphi)
- [9. Documentación](#9-documentaci%C3%B3n)
- [10. Resumen](#10-resumen)[^1]

***

## 1. Formato

### 1.1 Sangría

Usa 2 espacios por bloque lógico; evita tabs porque pueden renderizar diferente según el editor.[^1]

```pascal
procedure Example;
begin
  DoSomething;
  if Condition then
  begin
    DoSomethingElse;
  end;
end;
```


### 1.2 Longitud de línea

- Máximo 120 caracteres por línea.[^1]

El valor histórico de 80 caracteres viene de terminales de texto, pero 120 se ha vuelto estándar de facto por el balance entre legibilidad y contexto.  En monitores modernos esta longitud se considera práctica para la mayoría de desarrolladores Delphi.[^1]

Nota: Si usas un formateador automático y una guía vertical en el editor, mantén ambas configuraciones sincronizadas para evitar saltos de línea inconsistentes.[^1]

### 1.3 Comentarios

- `//` comentarios de una sola línea
- `{}` comentarios multilínea
- `(* *)` para código comentado temporalmente
- `///` comentarios de documentación XML[^1]

```pascal
// Comentario de una línea

{ Comentario
  multilínea }

(* Código deshabilitado temporalmente
   procedure OldMethod;
   begin
     // ...
   end; *)

/// <summary>
/// Comentario de documentación para un método
/// </summary>
/// <param name="AValue">Descripción del parámetro</param>
procedure DocumentedMethod(const AValue: string);
```


### 1.4 Directivas de compilador

En mayúsculas dentro de llaves, sin sangría; directivas anidadas pueden sangrarse para legibilidad.[^1]

```pascal
{$IFDEF DEBUG}
  {$IFDEF LOGGING}
  // Debug logging
  {$ENDIF}
// Código general de depuración
{$ENDIF}

{$REGION 'Private Methods'}
// Implementación
{$ENDREGION}
```


### 1.5 Sintaxis de sentencias

- Una sentencia por línea
- `begin`/`end` en sus propias líneas
- Prefiere `begin..end` incluso para sentencias simples, salvo cosas triviales como `raise`, `exit`, `continue`, `break`[^1]

```pascal
// Preferido - con begin..end
if Condition then
begin
  DoSomething;
end;

// Aceptable para sentencias simples
if HasError then
  raise Exception.Create('Error');

if not Found then
  Exit;

// Siempre usa begin..end para múltiples sentencias
if UserLoggedIn then
begin
  UpdateLastLogin;
  ShowDashboard;
end;
```


***

## 2. Convenciones de nombres

Usa PascalCase en todo el código (tipos, métodos, variables, constantes, parámetros).[^1]

### 2.1 Métodos

- Comienza con un verbo, sé descriptivo.[^1]

```pascal
// Procedures (acciones)
procedure SaveDocument;
procedure DrawRectangle;
procedure ValidateUserInput;

// Functions (devuelven valor)
function GetUserName: string;
function IsValidEmail(const AEmail: string): Boolean;
function CalculateTotalPrice: Currency;

// Evita nombres vagos
procedure DoSomething;  // Malo
procedure ProcessData;  // Mejor: ValidateAndSaveData
```


### 2.2 Parámetros

- Prefijo `A`, usa PascalCase.[^1]
- Usa `const` para parámetros inmutables.[^1]
- Usa explícitamente `var`/`out`.[^1]

```pascal
// Parámetros simples
procedure DrawRectangle(AX, AY: Integer);

// Parámetros const
procedure SaveToFile(const AFileName: string; const AData: TStringList);

// var para parámetros de salida
procedure GetUserInfo(const AUserID: Integer; var AName: string; var AEmail: string);

// out para parámetros de salida inicializados
procedure TryParseInteger(const AValue: string; out AResult: Integer; out ASuccess: Boolean);
```


### 2.3 Variables

- Locales: prefijo `L`.[^1]
- Campos: prefijo `F`.[^1]
- Globales: prefijo `G` (evitarlas).[^1]
- PascalCase, sin prefijo de tipo (excepto componentes).[^1]
- **Excepción**: contadores de bucles simples pueden usar letras minúsculas (`i`, `j`, `k`) sin prefijo.[^1]

```pascal
var
  LUserName: string;
  LCustomerList: TObjectList<TCustomer>;
  LIndex: Integer;
  LFound: Boolean;

// Contadores de bucle - excepción a la regla
for var i := 0 to 10 do
begin
  // Contador simple sin prefijo L
end;

// Bucles anidados
for var i := 0 to Rows - 1 do
begin
  for var j := 0 to Cols - 1 do
  begin
    Matrix[i, j] := 0;
  end;
end;
```

```pascal
type
  TMyClass = class
  private
    FConnectionString: string;
    FIsConnected: Boolean;
    FCustomers: TObjectList<TCustomer>;
  end;
```

```pascal
// Variables globales (¡evitar!)
var
  GAppTitle: string;
  GLogLevel: Integer;
```

**Excepción: variables globales internas a la unidad en la sección implementation**[^1]

Variables globales en `implementation` son aceptables para singletons internos o estado de la unidad.[^1]

```pascal
unit MyService;

interface

type
  TMyService = class
    class procedure Initialize;
    class procedure Finalize;
  end;

implementation

var
  GServiceInstance: TMyService;  // Interna a la unidad
  GInitialized: Boolean = False;

class procedure TMyService.Initialize;
begin
  if not GInitialized then
  begin
    GServiceInstance := TMyService.Create;
    GInitialized := True;
  end;
end;

class procedure TMyService.Finalize;
begin
  FreeAndNil(GServiceInstance);
  GInitialized := False;
end;

end.
```

**Ventajas:**

- No visible desde fuera (encapsulación).[^1]
- Ideal para singletons de unidad.[^1]
- Evita contaminación del namespace global.[^1]


#### 2.3.1 Nombres de componentes

- Usa el tipo del componente como prefijo (PascalCase).[^1]
- Sin prefijos `F`/`L`/`G` en instancias de componentes.[^1]
- Usa nombres descriptivos.[^1]

```pascal
// Componentes de UI
ButtonLogin: TButton;
ButtonCancel: TButton;
EditUserName: TEdit;
EditPassword: TEdit;
LabelWelcome: TLabel;
PanelHeader: TPanel;
GridCustomers: TStringGrid;

// Componentes de base de datos
QCustomers: TFDQuery;
QOrders: TFDQuery;
ConnectionMain: TFDConnection;

// Formularios y módulos
FormMain: TFormMain;
FormSettings: TFormSettings;
DMMain: TDataModule;
```

**Nota:** para convenciones de nombres de unidades relacionadas, ver [Sección 3.1].[^1]

### 2.4 Constantes

- Usa `c` para constantes generales, `sc` para constantes string.[^1]
- Usa MAYÚSCULAS solo para constantes de sistema/build.[^1]

```pascal
// Constantes técnicas
const
  cDefaultTimeout = 5000;
  cMaxRetryCount = 3;
  cBufferSize = 1024;
  cPI = 3.14159265359;

// Constantes de UI/cadenas
const
  scLoginErrorMessage = 'Invalid username or password.';
  scFormCaption = 'My Application';
  scConfirmDelete = 'Do you really want to delete this item?';

// Constantes de sistema (build)
const
  APP_VERSION = '1.2.3';
  BUILD_NUMBER = 12345;
  COMPANY_NAME = 'My Company Ltd';

// Resource strings (localizables)
resourcestring
  rsErrorFileNotFound = 'File not found.';
  rsConfirmExit = 'Do you want to exit the application?';
```


### 2.5 Tipos e interfaces

- Tipos: prefijo `T`.[^1]
- Interfaces: prefijo `I`.[^1]
- Excepciones: prefijo `E`.[^1]

```pascal
type
  // Clases (placeholders mínimos)
  TCustomer = class end;
  TOrderManager = class end;
  TDatabaseConnection = class end;

  // Clases sealed (utilidades)
  TPathUtils = class sealed end;
  TStringUtils = class sealed end;

  // Interfaces (placeholders)
  ILogger = interface end;
  IDataRepository = interface end;
  IEmailService = interface end;

  // Records (placeholders)
  TPoint3D = record end;
  TCustomerData = record end;

  // Enums (prefiere {$SCOPEDENUMS ON})
  TOrderStatus = (New, Processing, Completed, Cancelled);
  TLogLevel = (Debug, Info, Warning, Error);
```


***

## 3. Estructura de unidades

- Sección interface (declaraciones públicas).[^1]
- Sección implementation (implementación privada).[^1]
- Initialization/Finalization solo cuando sea necesario.[^1]
- Agrupa las cláusulas uses lógicamente.[^1]


### 3.1 Convenciones de nombres de unidad y jerarquía de namespace

Los proyectos modernos deben usar una jerarquía de namespaces consistente con estructura basada en contexto.[^1]

**Principios clave:**

- Nombres de unidad con estructura jerárquica usando punto.[^1]
- El nombre de archivo coincide con el de la unidad (`Main.Form.pas`).[^1]
- Formularios terminan en `.Form.pas`.[^1]
- Data modules terminan en `.DM.pas`.[^1]
- Jerarquías anidadas están permitidas y recomendadas.[^1]

**Ejemplos de componentes principales:**[^1]

```pascal
// Form principal
unit Main.Form;
// Archivo: Main.Form.pas
// Clase: TFormMain
// Instancia: FormMain

// Data module principal (típicamente conexión DB central)
unit Main.DM;
// Archivo: Main.DM.pas
// Clase: TDMMain
// Instancia: DMMain
```

**Ejemplos de jerarquías anidadas:**[^1]

```pascal
// Form de detalles de cliente
unit Customer.Details.Form;
// Archivo: Customer.Details.Form.pas
// Clase: TFormCustomerDetails
// Instancia: FormCustomerDetails

// Data module de detalles de cliente
unit Customer.Details.DM;
// Archivo: Customer.Details.DM.pas
// Clase: TDMCustomerDetails
// Instancia: DMCustomerDetails

// Otros ejemplos
unit Customer.List.Form;        // Lista de clientes
unit Customer.Edit.Form;        // Edición de clientes
unit Reports.Sales.Form;        // Reportes de ventas
unit Reports.Sales.DM;          // Data module para reportes de ventas
unit Settings.Database.Form;    // Configuración de base de datos
```

**Ventajas de esta estructura:**

- Asociación clara de unidades relacionadas.[^1]
- Mejor visión en proyectos grandes.[^1]
- Más fácil encontrar componentes relacionados.[^1]
- Evita conflictos de nombres.[^1]
- Agrupación lógica en Project Manager y uses.[^1]

**Convenciones de nombres para clases e instancias:**[^1]


| Nombre de unidad | Archivo | Nombre de clase | Nombre de instancia |
| :-- | :-- | :-- | :-- |
| `Main.Form` | `Main.Form.pas` | `TFormMain` | `FormMain` |
| `Main.DM` | `Main.DM.pas` | `TDMMain` | `DMMain` |
| `Customer.Details.Form` | `Customer.Details.Form.pas` | `TFormCustomerDetails` | `FormCustomerDetails` |
| `Customer.Details.DM` | `Customer.Details.DM.pas` | `TDMCustomerDetails` | `DMCustomerDetails` |

### 3.2 Ejemplo de estructura completa de unidad

```pascal
unit Customer.Manager;

interface

uses
  // System units
  System.SysUtils, System.Classes, System.Generics.Collections,
  // Database units
  FireDAC.Comp.Client, FireDAC.Stan.Param,
  // Own units
  Customer.Types, Database.Connection;

type
  TCustomerManager = class
  private
    FConnection: TDatabaseConnection;
    FCustomers: TObjectList<TCustomer>;
  public
    constructor Create(AConnection: TDatabaseConnection);
    destructor Destroy; override;
    procedure LoadCustomers;
    function FindCustomer(const AID: Integer): TCustomer;
  end;

implementation

uses
  // Units only needed in implementation
  System.StrUtils, System.DateUtils;

{ TCustomerManager }

constructor TCustomerManager.Create(AConnection: TDatabaseConnection);
begin
  inherited Create;
  FConnection := AConnection;
  FCustomers := TObjectList<TCustomer>.Create(True);
end;

destructor TCustomerManager.Destroy;
begin
  FreeAndNil(FCustomers);
  inherited;
end;

procedure TCustomerManager.LoadCustomers;
begin
  // Implementación
end;

function TCustomerManager.FindCustomer(const AID: Integer): TCustomer;
begin
  // Implementación
  Result := nil;
end;

end.
```


***

## 4. Estilo de código

### 4.1 Manejo de errores

- Usa `try..finally` para liberar recursos.[^1]
- Prefiere `FreeAndNil` sobre `.Free`.[^1]
- Usa `try..except` solo cuando puedas manejar el error de forma significativa.[^1]

```pascal
var
  LQuery: TFDQuery;
  LList: TObjectList<TObject>;
begin
  LQuery := nil;
  LList := nil;
  try
    LQuery := TFDQuery.Create(nil);
    LList := TObjectList<TObject>.Create(True);
    // Usar objetos
  finally
    FreeAndNil(LQuery);
    FreeAndNil(LList);
  end;
end;
```

Reglas adicionales:

- Nunca uses bloques `except` vacíos.[^1]

```pascal
// Ejemplo simple
LObject := TObject.Create;
try
  // usar objeto
finally
  FreeAndNil(LObject);
end;

// Manejo de excepciones
try
  RiskyOperation;
except
  on E: ESpecificException do
  begin
    LogError(E.Message);
    raise; // relanzar si es necesario
  end;
  on E: Exception do
  begin
    LogError('Unexpected error: ' + E.Message);
    // manejar o relanzar
  end;
end;
```

**Excepción: programación defensiva en sistemas críticos**[^1]

En sistemas críticos (manejadores de excepciones, logging, código de limpieza), a veces hay que suprimir errores para evitar recursión o caídas.[^1]

```pascal
// El manejador de excepciones no debe lanzar excepciones
procedure LogException(const E: Exception);
begin
  try
    WriteToLogFile(E.Message);
  except
    // Ignorar silenciosamente - el logging no debe fallar
    // Alternativa: fallback a OutputDebugString
  end;
end;

// Código de limpieza en finalization
finalization
  try
    if Assigned(GResolver) then
      FreeAndNil(GResolver);
  except
    // Ignorar silenciosamente - finalization debe completarse
  end;
end.
```

**Importante:** estos bloques `except` deben:

- Tener un comentario explicando por qué se suprimen errores.[^1]
- Usarse solo cuando sea absolutamente necesario.[^1]
- Preferentemente tener un mecanismo de fallback.[^1]


### 4.2 Ramificaciones y bucles

- Prefiere bloques `begin..end` por claridad incluso en ramas de una línea.[^1]
- Contadores de bucle pueden ser letras minúsculas (`i`, `j`, `k`) sin prefijo `L`.[^1]

```pascal
if UserLoggedIn then
begin
  ShowDashboard;
end
else
begin
  ShowLoginScreen;
end;

case DayOfTheWeek(Date) of // 1=Lunes .. 7=Domingo
  1: DoMondayRoutine;
  2: DoTuesdayRoutine;
  // ...
end;

// Contador simple
for var i := 1 to 10 do
begin
  if SomeCondition then
    Break; // sin begin..end adicional
end;

// Declaración tradicional
var
  i: Integer;
begin
  for i := 0 to List.Count - 1 do
  begin
    ProcessItem(List[i]);
  end;
end;
```


### 4.3 Números y tipos

- Evita igualdad directa de floats (`=`); usa un epsilon.[^1]
- Usa `Double`/`Single` cuando no se requiere precisión exacta.[^1]
- Usa `Currency` o `TBcd` para cálculos monetarios.[^1]
- Usa `Variant` solo cuando sea técnicamente requerido (por ejemplo COM).[^1]

***

## 5. Propiedades y getters/setters

- Solo agrega getters/setters si se necesita lógica adicional.[^1]
- Campos (`F...`) deben ser `private`; getters/setters idealmente `protected`.[^1]

```pascal
type
  TMyClass = class
  private
    FName: string;
    FAge: Integer;
  protected
    function GetName: string; virtual;
    procedure SetName(const AValue: string); virtual;
    function GetDisplayName: string; virtual;
  public
    property Name: string read GetName write SetName;
    property Age: Integer read FAge write FAge;
    property DisplayName: string read GetDisplayName;
  end;

implementation

function TMyClass.GetName: string;
begin
  Result := FName;
end;

procedure TMyClass.SetName(const AValue: string);
begin
  if FName <> AValue then
  begin
    FName := Trim(AValue);
    // Validación o notificación adicional
  end;
end;

function TMyClass.GetDisplayName: string;
begin
  Result := Format('%s (%d years)', [FName, FAge]);
end;
```


***

## 6. Eventos

- Nombres de eventos comienzan con `On`.[^1]
- Manejadores deben comenzar con `Do...` y delegar a la lógica de negocio.[^1]

```pascal
// Manejador que delega a la lógica
procedure TForm1.ButtonLoginClick(Sender: TObject);
begin
  DoLogin;
end;

procedure TForm1.ButtonCancelClick(Sender: TObject);
begin
  DoCancel;
end;

// Lógica de negocio en métodos separados
procedure TForm1.DoLogin;
begin
  if ValidateLoginData then
  begin
    AuthenticateUser;
    ShowMainForm;
  end
  else
  begin
    ShowMessage(scLoginErrorMessage);
  end;
end;

procedure TForm1.DoCancel;
begin
  if ConfirmExit then
    Close;
end;
```


***

## 7. Misceláneo

### 7.1 `with`

Evita la sentencia `with`.[^1]

```pascal
// Evitar
with Customer do
begin
  Name := 'Max';
  Address := 'Example Street';
end;

// Preferir
Customer.Name := 'Max';
Customer.Address := 'Example Street';
```


### 7.2 Records

- Usa records para estructuras de datos simples y autocontenidas.[^1]
- Prefijo `T` en nombres de records.[^1]
- **Los campos de records NO llevan prefijos** (sin `F`, `L`, `G`), siempre son públicos.[^1]
- Evita lógica compleja en records salvo helpers o records con métodos.[^1]
- Son ideales para DTOs, tipos geométricos o value objects simples.[^1]

```pascal
type
  TPoint = record
    X, Y: Integer;  // Sin prefijo F
  end;

  TStackFrameInfo = record
    ModuleName: string;
    ProcName: string;
    FileName: string;
    Line: Integer;
    Address: Pointer;
  end;
```

**Comparación clase vs record:**[^1]

```pascal
// Clase - campos privados con prefijo F
type
  TCustomer = class
  private
    FName: string;
    FAge: Integer;
  public
    property Name: string read FName write FName;
    property Age: Integer read FAge write FAge;
  end;

// Record - campos públicos sin prefijo
type
  TCustomerData = record
    Name: string;  // Sin prefijo
    Age: Integer;
  end;
```


### 7.2a Clases sealed para utilidades

Para clases de utilidades puras sin instancias ni estado, usa `class sealed` con métodos de clase.[^1]

**Cuándo usar `sealed class` en lugar de `record`:**

- **Record**: estructuras de datos (DTOs, value objects).[^1]
- **Clase sealed**: funciones de utilidad sin datos.[^1]

```pascal
type
  TPoint = record
    X, Y: Integer;
  end;

type
  TPathUtils = class sealed
    class function FileExists(const APath: string): Boolean;
    class procedure DeleteFile(const APath: string);
  end;
```

**Ventajas de `sealed` para utilidades:**

- Evita herencia sin sentido.[^1]
- Comunica intención de diseño: “solo funciones, sin instancias”.[^1]
- Permite optimizaciones del compilador.[^1]
- Sigue buenas prácticas de otros lenguajes (C\# `static class`, Java `final class`).[^1]

**Comparación:**[^1]


| Aspecto | Record | Clase sealed | Clase normal |
| :-- | :-- | :-- | :-- |
| Propósito | Estructura de datos | Funciones de utilidad | Objetos con estado |
| Instancias | Semántica valor | Ninguna (solo class methods) | Semántica referencia |
| Herencia | No | No (sealed) | Sí (posible) |
| Ejemplo | `TPoint`, `TRect` | `TPathUtils`, `TDXStacktrace` | `TCustomer`, `TOrder` |

### 7.3 Class y record helpers

Los helpers extienden tipos existentes con métodos adicionales sin modificar el tipo original.[^1]

**Convención de nombres:**

- Formato: `T<TypeName>Helper`.[^1]
- La palabra **Helper** se reserva para class/record helpers reales.[^1]
- No usar “Helper” para clases de utilidades sealed.[^1]

```pascal
// Correcto - Record Helper
type
  TPointHelper = record helper for TPoint
    function Distance(const AOther: TPoint): Double;
    function ToString: string;
  end;

// Correcto - Class Helper
type
  TStringListHelper = class helper for TStringList
    procedure SaveToFileUTF8(const AFileName: string);
    function ContainsText(const AText: string): Boolean;
  end;

// INCORRECTO - no es helper, solo utilidades
type
  TFileHelper = class sealed  // Debe ser TFileUtils o similar
    class function FileExists(const APath: string): Boolean;
  end;

// Correcto - clase sealed de utilidades
type
  TFileUtils = class sealed
    class function FileExists(const APath: string): Boolean;
    class procedure DeleteFile(const APath: string);
  end;
```

**Cuándo usar helpers:**

- Extender tipos RTL/VCL/FMX sin herencia.[^1]
- Agregar métodos de conveniencia a records.[^1]
- Compatibilidad hacia atrás cuando no puedes modificar el tipo original.[^1]

**Notas importantes:**

- Solo un helper puede estar activo por tipo en un ámbito dado.[^1]
- No pueden agregar campos, solo métodos.[^1]
- Tienen acceso a miembros privados del tipo extendido.[^1]


### 7.4 Enumeraciones

- Prefijo `T` en tipos enum.[^1]
- Prefiere notación con punto y `{$SCOPEDENUMS ON}`.[^1]

```pascal
type
  TOrderStatus = (New, Processing, Completed);
var
  LStatus: TOrderStatus;
begin
  LStatus := TOrderStatus.New;
end;
```


### 7.5 Seguridad de hilos

Prefiere `TMonitor` para sincronización en escenarios simples.[^1]

```pascal
procedure TMyObject.AddCustomer(const ACustomer: TCustomer);
begin
  TMonitor.Enter(Self);
  try
    FCustomers.Add(ACustomer);
  finally
    TMonitor.Exit(Self);
  end;
end;
```


***

## 8. Funciones modernas de Delphi

### 8.1 Genéricos

Usa genéricos para colecciones type-safe y algoritmos reutilizables.[^1]

```pascal
var
  LCustomers: TObjectList<TCustomer>;
  LNames: TList<string>;
  LLookup: TDictionary<string, TCustomer>;

function FindItem<T>(const AList: TList<T>; const APredicate: TFunc<T, Boolean>): T;
var
  LItem: T;
begin
  for LItem in AList do
  begin
    if APredicate(LItem) then
      Exit(LItem);
  end;
  Result := Default(T);
end;
```


#### 8.1.1 TArray<T> vs TList<T> vs TObjectList<T>

Elige la colección correcta según el caso de uso.[^1]

**TArray<T>** – para colecciones con tamaño fijo o poco cambiante.[^1]

```pascal
type
  TStacktrace = TArray<TStackFrameInfo>;  // Tamaño fijo tras captura

function GetTopCustomers: TArray<TCustomer>;  // Valor de retorno

var
  LNames: TArray<string>;
begin
  SetLength(LNames, 3);
  LNames[^0] := 'Alice';   // O(1), muy rápido
  LNames[^1] := 'Bob';
  LNames[^2] := 'Charlie';
  LNames[^0] := 'John';    // Modificar también es O(1)
end;
```

**Ventajas:**

- Poca sobrecarga de memoria.[^1]
- Acceso rápido por índice y modificación in-place.[^1]
- Ideal para valores de retorno y colecciones de tamaño fijo.[^1]
- Liberación automática.[^1]

**TList<T>** – para listas dinámicas de tipos valor.[^1]

```pascal
var
  LNumbers: TList<Integer>;
  LNames: TList<string>;
begin
  LNumbers := TList<Integer>.Create;
  try
    LNumbers.Add(42);
    LNumbers.Add(100);
  finally
    FreeAndNil(LNumbers);
  end;
end;
```

**Ventajas:**

- Añadir/eliminar dinámicamente.[^1]
- Métodos integrados de ordenación y búsqueda.[^1]
- Ideal para tipos valor (Integer, String, Records).[^1]

**TObjectList<T>** – para listas de objetos con ownership.[^1]

```pascal
var
  LCustomers: TObjectList<TCustomer>;
begin
  LCustomers := TObjectList<TCustomer>.Create(True);  // True = OwnsObjects
  try
    LCustomers.Add(TCustomer.Create('Max'));
    // Los objetos se liberan automáticamente
  finally
    FreeAndNil(LCustomers);
  end;
end;
```

**Ventajas:**

- Manejo automático de memoria para objetos (OwnsObjects=True).[^1]
- Previene memory leaks.[^1]
- Ideal para colecciones de objetos.[^1]

**Guía de decisión:**[^1]


| Criterio | TArray<T> | TList<T> | TObjectList<T> |
| :-- | :-- | :-- | :-- |
| Cambios de tamaño | Raros (SetLength) | Frecuentes (Add/Delete) | Frecuentes (Add/Delete) |
| Modificación | Muy rápida (O(1)) | Rápida (O(1)) | Rápida (O(1)) |
| Contenido | Cualquier tipo | Tipos valor | Objetos |
| Ownership | N/A | N/A | Sí (OwnsObjects) |
| Overhead mem. | Mínimo | Medio | Medio |
| Caso de uso | Tamaño fijo, retornos | Listas dinámicas | Colecciones de objetos |

### 8.2 Métodos anónimos

Útiles para funcionalidad local corta.[^1]

```pascal
var
  LButton: TButton;
begin
  LButton := TButton.Create(Self);
  LButton.OnClick := procedure(Sender: TObject)
    begin
      ShowMessage('Button clicked');
    end;
end;
```


### 8.3 Variables inline (Delphi 10.3+)

Declara variables en el punto de uso para mejorar la legibilidad; en bucles, prefiere declaración inline.[^1]

```pascal
// Tradicional
procedure ProcessData;
var
  i: Integer;
  LCustomer: TCustomer;
begin
  for i := 0 to CustomerList.Count - 1 do
  begin
    LCustomer := CustomerList[i];
    // ...
  end;
end;

// Con variables inline (preferido 10.3+)
procedure ProcessData;
begin
  for var i := 0 to CustomerList.Count - 1 do
  begin
    var LCustomer := CustomerList[i];
    // ...
  end;

  // También útil para otras variables
  var LResult := CalculateSomething;
  if LResult > 0 then
    ProcessResult(LResult);
end;
```


### 8.4 Cadenas multilínea (Delphi 12+)

Usa comillas triples `'''` con aperturas y cierres en líneas propias; la indentación del cierre define la base.[^1]

```pascal
// Tradicional - difícil de leer
const
  SQL_QUERY = 'SELECT c.id, c.name, c.email, o.order_date ' +
              'FROM customers c ' +
              'LEFT JOIN orders o ON c.id = o.customer_id ' +
              'WHERE c.active = 1 ' +
              'ORDER BY c.name';

// Cadenas multilínea (Delphi 12+)
const
  SQL_QUERY = '''
SELECT c.id, c.name, c.email, o.order_date
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE c.active = 1
ORDER BY c.name
''';
```


### 8.5 Atributos

Usa atributos para metadatos y configuración.[^1]

```pascal
type
  [Table('customers')]
  TCustomer = class
  private
    [Column('id', True)] // True = Primary Key
    FID: Integer;

    [Column('name')]
    [Required]
    [MaxLength(100)]
    FName: string;

    [Column('email')]
    [Email]
    FEmail: string;
  public
    property ID: Integer read FID write FID;
    property Name: string read FName write FName;
    property Email: string read FEmail write FEmail;
  end;
```


***

## 9. Documentación

Usa comentarios XML (`///`) de forma consistente para **todas** las APIs públicas:[^1]

- Clases, records e interfaces públicas.
- Métodos y funciones públicas.
- Propiedades públicas.
- Tipos y constantes públicas.[^1]

**Niveles de documentación:**

- **Mínimo:** `<summary>` en todos los elementos públicos.[^1]

```
- **Óptimo:** además `<param>`, `<returns>`, `<exception>`, `<remarks>` cuando corresponda.[^1]
```

```pascal
/// <summary>
/// Calcula la distancia entre dos puntos.
/// </summary>
/// <param name="APoint1">Primer punto</param>
/// <param name="APoint2">Segundo punto</param>
/// <returns>Distancia como Double</returns>
/// <exception cref="EArgumentException">
/// Se lanza cuando alguno de los puntos es nil.
/// </exception>
function CalculateDistance(const APoint1, APoint2: TPoint): Double;

/// <summary>
/// Representa un cliente en el sistema
/// </summary>
/// <remarks>
/// Esta clase encapsula todos los datos y operaciones del cliente.
/// Usa el método fábrica CreateCustomer para instanciar.
/// </remarks>
type
  TCustomer = class
  private
    FID: Integer;
    FName: string;
  public
    /// <summary>ID único de cliente</summary>
    property ID: Integer read FID write FID;

    /// <summary>Nombre completo del cliente</summary>
    property Name: string read FName write FName;
  end;

/// <summary>
/// Información sobre un frame de stack
/// </summary>
type
  TStackFrameInfo = record
    /// <summary>Nombre del módulo (EXE/DLL)</summary>
    ModuleName: string;
    /// <summary>Nombre del procedimiento/función</summary>
    ProcName: string;
    /// <summary>Ruta del archivo fuente</summary>
    FileName: string;
    /// <summary>Número de línea en el archivo</summary>
    Line: Integer;
  end;
```


***

## 10. Resumen

- Formato, nombres y estructura consistentes.[^1]
- Organización clara de unidades y separación de responsabilidades.[^1]
- Uso de funciones modernas: genéricos, métodos anónimos, variables inline (10.3+), cadenas multilínea (12+), atributos.[^1]

***

## Licencia

MIT License
https://opensource.org/licenses/MIT[^1]

<div align="center">⁂</div>

[^1]: Delphi-Style-Guide-EN.md

