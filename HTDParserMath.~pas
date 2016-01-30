unit HTDParserMath;

interface

uses
  SysUtils, Classes, Math, DB, DBClient;

type

  TValueType = (Unknown, OpenOperand, CloseOperand, NumericValue, MinusMathOperand,
    PlusMathOperand, MultiplyMathOperand, DivideMathOperand, EmptyCreation, SinMathDegOperand, CosMathDegOperand,
    TanMathDegOperand, ExpMathOperand, SinMathRadOperand, CosMathRadOperand,
    TanMathRadOperand);

  PHTDParserNode = ^THTDParserNode;

  THTDParserNode = record
    Resultado: Extended;
    NumValue: Extended;
    ValueType: TValueType;
    P_FDir: PHTDParserNode;
    P_FEsq: PHTDParserNode;
    P_Pai: PHTDParserNode;
  end;

  THTDParser = class(TComponent)
  private
    fTexto: string;
    fAnswer: Extended;
    fMyVarsDataset: TClientDataSet;
    NextNegative: boolean;
    fIndeterminate: boolean;
    function GetTexto: string;
    procedure SetTexto(txt: string);
    function GetAnswer: Extended;
    function ValidateText: Boolean;
    procedure ExecuteParseAndCalc;
    function IsOpenOperand(t: string): boolean;
    function IsCloseOperand(t: string): boolean;
    function IsNumericValue(t: string): boolean;
    function IsMinusMathOperand(t: string): boolean;
    function IsPlusMathOperand(t: string): boolean;
    function IsDivideMathOperand(t: string): boolean;
    function IsMultiplyMathOperand(t: string): boolean;
    function IsVariableOperand(t: string): boolean;
    function IsExpMathOperand(t: string): boolean;
    function IsSinMathDegOperand(t: string): boolean;
    function IsCosMathDegOperand(t: string): boolean;
    function IsTanMathDegOperand(t: string): boolean;
    function IsSinMathRadOperand(t: string): boolean;
    function IsCosMathRadOperand(t: string): boolean;
    function IsTanMathRadOperand(t: string): boolean;

    function GetVariableFromName(VarName: string; var VarVal: Extended): boolean;
    function GetNextValueFromText(var Text: string; var ResultText: string; var ResType: TValueType): boolean;
    function AddNewValueOnTree(ControlRecord: PHTDParserNode; InputValue: Extended; ResType: TValueType; IsTheEnd: Boolean): PHTDParserNode;
    function FindFather(ControlRecord: PHTDParserNode; CreateCleanNode: boolean = false): PHTDParserNode;
    procedure CompletaGrid(ControlRecord: PHTDParserNode);
    function CalculateByTree(ControlRecord: PHTDParserNode): Extended;
    function PrintTree(ControlRecord: PHTDParserNode): string;
  public
    constructor Create;
    destructor Destroy; override;
    procedure Calculate;
    procedure AddNewVariable(VarName: string; VarVal: Extended);
  published
    property Texto: string read GetTexto write SetTexto;
    property Answer: Extended read GetAnswer;
    property Indeterminate: boolean read fIndeterminate;

  end;

const
  SCloseOperand = ')';
  SOpenOperand = '(';
  SPlusOperand = '+';
  SMinusOperand = '-';
  SMultiplyOperand = '*';
  SDivideOperand = '/';
  SVariableOperand = ':';
  SSinMathDegOperand = 'S';
  SCosMathDegOperand = 'C';
  STanMathDegOperand = 'T';
  SSinMathRadOperand = 's';
  SCosMathRadOperand = 'c';
  STanMathRadOperand = 't';
  SExpMathOperand = '^';
  ExcParseErr = 'Exception parse Error - Cheque Sua Expressão';

procedure Register;

implementation

uses TypInfo;

{ THTDParser }

procedure Register;
begin
  RegisterComponents('HTD Componentes', [THTDParser]);
end;

function THTDParser.GetTexto: string;
begin
  result := fTexto;
end;

procedure THTDParser.SetTexto(txt: string);
begin
  fTexto := txt;
end;

function THTDParser.GetAnswer: Extended;
begin
  result := fAnswer;
end;

procedure THTDParser.Calculate;
begin
  if ValidateText then
    ExecuteParseAndCalc
  else
    raise Exception.Create('Invalid String To Parse');
end;

function THTDParser.GetVariableFromName(VarName: string; var VarVal: Extended): boolean;
begin
  result := false;
  if fMyVarsDataset.Locate('VAR', VarName, [loCaseInsensitive]) then
  begin
    result := True;
    VarVal := fMyVarsDataset.fieldbyname('VLR').AsFloat;
  end;
end;

function THTDParser.IsCloseOperand(t: string): boolean;
begin
  result := false;
  if t = scloseoperand then
    result := true;
end;

function THTDParser.IsMultiplyMathOperand(t: string): boolean;
begin
  result := false;
  if t = sMultiplyOperand then
    result := true;
end;

function THTDParser.IsPlusMathOperand(t: string): boolean;
begin
  result := false;
  if t = sPlusOperand then
    result := true;

end;

function THTDParser.IsVariableOperand(t: string): boolean;
begin
  result := false;
  if t = svariableoperand then
    result := true;
end;

function THTDParser.IsNumericValue(t: string): boolean;
begin
  result := false;
  if ((t = ',') or (t[1] in ['0'..'9'])) then
    result := true;
end;

function THTDParser.IsExpMathOperand(t: string): boolean;
begin
  result := false;
  if t = sExpMathOperand then
    result := true;
end;

function THTDParser.IsCosMathDegOperand(t: string): boolean;
begin
  result := false;
  if t = sCosMathDegOperand then
    result := true;
end;

function THTDParser.IsSinMathDegOperand(t: string): boolean;
begin
  result := false;
  if t = sSinMathDegOperand then
    result := true;
end;

function THTDParser.IsTanMathDegOperand(t: string): boolean;
begin
  result := false;
  if t = sTanMathDegOperand then
    result := true;
end;

function THTDParser.IsCosMathRadOperand(t: string): boolean;
begin
  result := false;
  if t = sCosMathRadOperand then
    result := true;
end;

function THTDParser.IsSinMathRadOperand(t: string): boolean;
begin
  result := false;
  if t = sSinMathRadOperand then
    result := true;
end;

function THTDParser.IsTanMathRadOperand(t: string): boolean;
begin
  result := false;
  if t = sTanMathRadOperand then
    result := true;
end;

function THTDParser.IsOpenOperand(t: string): boolean;
begin
  result := false;
  if t = sOpenOperand then
    result := true;
end;

function THTDParser.IsDivideMathOperand(t: string): boolean;
begin
  result := false;
  if t = sDivideOperand then
    result := true;
end;

function THTDParser.IsMinusMathOperand(t: string): boolean;
begin
  result := false;
  if t = sMinusOperand then
    result := true; //
end;

function THTDParser.GetNextValueFromText(var Text, ResultText: string;
  var ResType: TValueType): boolean;
var
  TxRes: string;
  ValVar: Extended;
begin
  Text := trim(text);
  result := false;
  if Text <> '' then
  begin
    if IsOpenOperand(Text[1]) then
    begin
      ResultText := SOpenOperand;
      ResType := OpenOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsCloseOperand(Text[1]) then
    begin
      ResultText := SCloseOperand;
      ResType := CloseOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsMinusMathOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := MinusMathOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsPlusMathOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := PlusMathOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsMultiplyMathOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := MultiplyMathOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsCosMathDegOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := CosMathDegOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsSinMathDegOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := SinMathDegOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsTanMathDegOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := TanMathDegOperand;
      Delete(text, 1, 1);
      result := true;
    end

    else if IsCosMathRadOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := CosMathRadOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsSinMathRadOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := SinMathRadOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsTanMathRadOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := TanMathRadOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsExpMathOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := ExpMathOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsDivideMathOperand(Text[1]) then
    begin
      ResultText := Text[1];
      ResType := DivideMathOperand;
      Delete(text, 1, 1);
      result := true;
    end
    else if IsNumericValue(Text[1]) then
    begin
      TxRes := '';
      while IsNumericValue(Text[1]) do
      begin
        TxRes := TxRes + Text[1];
        Delete(text, 1, 1);
        if text = '' then
          break;
      end;
      ResultText := TxRes;
      ResType := NumericValue;
      result := true;
    end
    else if IsVariableOperand(Text[1]) then
    begin
      TxRes := '';
      Delete(text, 1, 1);
      while not ((IsMultiplyMathOperand(Text[1])) or
        (IsDivideMathOperand(Text[1])) or
        (IsMinusMathOperand(Text[1])) or
        (IsPlusMathOperand(Text[1])) or
        (IsCloseOperand(Text[1])) or
        (IsSinMathDegOperand(Text[1])) or
        (IsSinMathRadOperand(Text[1])) or
        (IsCosMathDegOperand(Text[1])) or
        (IsCosMathRadOperand(Text[1])) or
        (IsTanMathDegOperand(Text[1])) or
        (IsTanMathRadOperand(Text[1])) or
        (IsExpMathOperand(Text[1])) or
        (IsOpenOperand(Text[1]))) do
      begin
        TxRes := TxRes + Text[1];
        Delete(text, 1, 1);
      end;
      TxRes := Trim(TxRes);
      if GetVariableFromName(TxRes, ValVar) then
      begin
        ResultText := CurrToStr(ValVar);
        ResType := NumericValue;
        result := true;
      end
      else
      begin
        ResultText := '';
        ResType := Unknown;
        result := False;
        raise Exception.Create('Var: ' + txRes + ' not Found');
      end;
    end
    else
    begin
      ResultText := '';
      ResType := Unknown;
      result := False;
    end;
  end;
end;

constructor THTDParser.Create;
begin
  inherited Create(nil);
  fMyVarsDataset := TClientDataSet.Create(nil);
  fMyVarsDataset.FieldDefs.Add('VAR', ftString, 10, true);
  fMyVarsDataset.FieldDefs.Add('VLR', ftFloat, 0, true);
  fMyVarsDataset.CreateDataSet;
  fMyVarsDataset.Open;
  fIndeterminate := false;

end;

destructor THTDParser.Destroy;
begin
  fMyVarsDataset.Close;
  FreeAndNil(fMyVarsDataset);
  inherited Destroy;
end;

function THTDParser.ValidateText: Boolean;
var
  st: string;
begin
  result := true;
  fTexto := StringReplace(fTexto, '1s', 's', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, '1c', 'c', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, '1t', 't', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, '1S', 'S', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, '1C', 'C', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, '1T', 'T', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 's', '1s', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'c', '1c', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 't', '1t', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'S', '1S', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'C', '1C', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'T', '1T', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, ':x', 'x', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'x', ':x', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, ':X', 'X', [rfReplaceAll]);
  fTexto := StringReplace(fTexto, 'X', ':X', [rfReplaceAll]);

  if fTexto = '' then
    result := false;
  if Result then
  begin
    st := copy(ftexto, 1, 1);
    fTexto := '(' + Trim(fTexto) + ')';
  end;
end;

procedure THTDParser.AddNewVariable(VarName: string; VarVal: Extended);
begin
  with fMyVarsDataset do
  begin
    if not fMyVarsDataset.Locate('VAR', VarName, [loCaseInsensitive]) then
    begin
      Insert;
      FieldByName('VAR').AsString := VarName;
      FieldByName('VLR').AsFloat := VarVal;
    end
    else
    begin
      Edit;
      FieldByName('VAR').AsString := VarName;
      FieldByName('VLR').AsFloat := VarVal;
    end;
    Post;
  end;
end;

procedure THTDParser.ExecuteParseAndCalc;
var
  TxAtu, TxNew, r: string;
  RType: TValueType;
  ControlRecord: PHTDParserNode;
  ArvIni: THTDParserNode;
  Tam: integer;
begin
  TxAtu := Texto;
  TxNew := '';
  RType := Unknown;

  GetMem(ControlRecord, SizeOf(THTDParserNode));
  ControlRecord^.ValueType := EmptyCreation;
  ControlRecord^.NumValue := 0;
  ControlRecord^.P_FDir := nil;
  ControlRecord^.P_FEsq := nil;
  ControlRecord^.P_Pai := nil;

  //daqui pra baixo deve montar a arvore e fazer a conta
  NextNegative := false;
  while GetNextValueFromText(TxAtu, r, RType) do
  begin
    TxNew := TxNew + ',' + r;
    ControlRecord := AddNewValueOnTree(ControlRecord, StrToCurrDef(r, 0), Rtype, (Trim(TxAtu) = ''));
  end;
  ControlRecord := FindFather(ControlRecord);
  if TxAtu <> '' then
    raise Exception.Create('Formula incorreta, Avalie sua Formula');

  CompletaGrid(ControlRecord);
  //delete(txnew,1,1);
  //SetTexto(txnew);

  ControlRecord := FindFather(ControlRecord);
  SetTexto(PrintTree(ControlRecord));
  fAnswer := CalculateByTree(ControlRecord);

end;

function THTDParser.AddNewValueOnTree(ControlRecord: PHTDParserNode;
  InputValue: Extended; ResType: TValueType; IsTheEnd: Boolean): PHTDParserNode;
begin
  if ControlRecord^.ValueType = Unknown then
    raise exception.Create('Unknow Parameter Type Found');

  if ControlRecord^.ValueType = EmptyCreation then
  begin
    ControlRecord^.ValueType := ResType;
    ControlRecord^.NumValue := InputValue;
  end
  else
  begin
    if ResType in [OpenOperand, NumericValue] then
    begin
      if ControlRecord^.P_FEsq = nil then
      begin
        if NextNegative then
        begin
          if ResType in [NumericValue] then
            InputValue := InputValue * (-1)
          else
          begin
            GetMem(ControlRecord^.P_FDir, SizeOf(THTDParserNode));
            ControlRecord^.P_FDir^.ValueType := MinusMathOperand;
            ControlRecord^.P_FDir^.NumValue := 0;
            ControlRecord^.P_FDir^.P_FDir := nil;
            ControlRecord^.P_FDir^.P_FEsq := nil;
            ControlRecord^.P_FDir^.P_Pai := ControlRecord;
            ControlRecord := ControlRecord^.P_FDir;
          end;
          NextNegative := false;
        end;

        GetMem(ControlRecord^.P_FEsq, SizeOf(THTDParserNode));
        ControlRecord^.P_FEsq^.ValueType := ResType;
        ControlRecord^.P_FEsq^.NumValue := InputValue;
        ControlRecord^.P_FEsq^.P_FDir := nil;
        ControlRecord^.P_FEsq^.P_FEsq := nil;
        ControlRecord^.P_FEsq^.P_Pai := ControlRecord;

        if ResType = OpenOperand then
          ControlRecord := ControlRecord^.P_FEsq;
      end
      else
        raise Exception.Create(ExcParseErr);
    end;

    if ResType in [CloseOperand, MinusMathOperand, PlusMathOperand,
      MultiplyMathOperand, DivideMathOperand, SinMathDegOperand, CosMathDegOperand,
      TanMathDegOperand, ExpMathOperand, SinMathRadOperand, CosMathRadOperand,
      TanMathRadOperand] then
    begin
      if (ControlRecord^.P_FDir = nil) then
      begin
        if ResType = MinusMathOperand then
        begin
          NextNegative := true;
          ResType := PlusMathOperand;
        end;


        if ((ControlRecord^.ValueType in [TanMathRadOperand, SinMathDegOperand, CosMathDegOperand,
          TanMathDegOperand, ExpMathOperand, SinMathRadOperand, CosMathRadOperand]) and (ResType in [MinusMathOperand,
            PlusMathOperand, MultiplyMathOperand, DivideMathOperand])) then
        begin
          ControlRecord := FindFather(ControlRecord, true);
          GetMem(ControlRecord^.P_FDir, SizeOf(THTDParserNode));
          ControlRecord^.P_FDir^.ValueType := ResType;
          ControlRecord^.P_FDir^.NumValue := InputValue;
          ControlRecord^.P_FDir^.P_FDir := nil;
          ControlRecord^.P_FDir^.P_FEsq := nil;
          ControlRecord^.P_FDir^.P_Pai := ControlRecord;
          ControlRecord := ControlRecord^.P_FDir;
        end
        else if ((ControlRecord^.ValueType in [MultiplyMathOperand, DivideMathOperand]) and (ResType in [MinusMathOperand, PlusMathOperand])) then
        begin
          ControlRecord := FindFather(ControlRecord, true);
          GetMem(ControlRecord^.P_FDir, SizeOf(THTDParserNode));
          ControlRecord^.P_FDir^.ValueType := ResType;
          ControlRecord^.P_FDir^.NumValue := InputValue;
          ControlRecord^.P_FDir^.P_FDir := nil;
          ControlRecord^.P_FDir^.P_FEsq := nil;
          ControlRecord^.P_FDir^.P_Pai := ControlRecord;
          ControlRecord := ControlRecord^.P_FDir;
        end
        else
        begin
          GetMem(ControlRecord^.P_FDir, SizeOf(THTDParserNode));
          ControlRecord^.P_FDir^.ValueType := ResType;
          ControlRecord^.P_FDir^.NumValue := InputValue;
          ControlRecord^.P_FDir^.P_FDir := nil;
          ControlRecord^.P_FDir^.P_FEsq := nil;
          ControlRecord^.P_FDir^.P_Pai := ControlRecord;

          if ((ResType = CloseOperand) and not (IsTheEnd)) then
            ControlRecord := FindFather(ControlRecord, true)
          else
            ControlRecord := ControlRecord^.P_FDir;
        end;
      end
      else
        raise Exception.Create(ExcParseErr);
    end;
  end;
  result := ControlRecord;
end;

function THTDParser.FindFather(ControlRecord: PHTDParserNode;
  CreateCleanNode: boolean = false): PHTDParserNode;
begin

  if ControlRecord^.P_Pai <> nil then
  begin
    result := ControlRecord^.P_Pai;
    if Result^.P_FDir <> nil then
      Result := FindFather(result, CreateCleanNode);
  end
  else
  begin
    if CreateCleanNode then
    begin
      GetMem(ControlRecord^.P_Pai, SizeOf(THTDParserNode));
      ControlRecord^.P_Pai^.ValueType := OpenOperand;
      ControlRecord^.P_Pai^.NumValue := 0;
      ControlRecord^.P_Pai^.P_FDir := nil;
      ControlRecord^.P_Pai^.P_FEsq := ControlRecord;
      ControlRecord^.P_Pai^.P_Pai := nil;
      Result := ControlRecord^.P_Pai;
    end
    else
      result := ControlRecord;
  end;
end;

procedure THTDParser.CompletaGrid(ControlRecord: PHTDParserNode);
begin

  if ((ControlRecord^.P_FEsq <> nil) or (ControlRecord^.P_FDir <> nil)) then
  begin
    if ControlRecord^.P_FEsq <> nil then
      CompletaGrid(ControlRecord^.P_FEsq)
    else
    begin
      GetMem(ControlRecord^.P_FEsq, SizeOf(THTDParserNode));
      ControlRecord^.P_FEsq^.ValueType := NumericValue;
      ControlRecord^.P_FEsq^.NumValue := 0;
      ControlRecord^.P_FEsq^.P_FDir := nil;
      ControlRecord^.P_FEsq^.P_FEsq := nil;
      ControlRecord^.P_FEsq^.P_Pai := ControlRecord;
    end;

    if ControlRecord^.P_FDir <> nil then
      CompletaGrid(ControlRecord^.P_FDir)
    else
    begin
      GetMem(ControlRecord^.P_FDir, SizeOf(THTDParserNode));
      ControlRecord^.P_FDir^.ValueType := CloseOperand;
      ControlRecord^.P_FDir^.NumValue := 0;
      ControlRecord^.P_FDir^.P_FDir := nil;
      ControlRecord^.P_FDir^.P_FEsq := nil;
      ControlRecord^.P_FDir^.P_Pai := ControlRecord;
    end;
  end;
end;

function THTDParser.CalculateByTree(ControlRecord: PHTDParserNode): Extended;
var
  VDir, VEsq: Extended;
  SDir: TValueType;
begin
  if ControlRecord <> nil then
  begin
    if ControlRecord^.ValueType = NumericValue then
    begin
      ControlRecord^.Resultado := ControlRecord^.NumValue;
      result := ControlRecord^.Resultado;
    end
    else
    begin
      VEsq := CalculateByTree(ControlRecord^.P_FEsq);
      Vdir := CalculateByTree(ControlRecord^.P_FDir);

      if ((ControlRecord^.P_FDir <> nil) and (ControlRecord^.P_FDir^.ValueType <> CloseOperand)) then
        SDir := ControlRecord^.P_FDir^.ValueType
      else
        SDir := PlusMathOperand;

      case SDir of
        MinusMathOperand: ControlRecord^.Resultado := VEsq - Vdir;
        PlusMathOperand: ControlRecord^.Resultado := VEsq + Vdir;
        DivideMathOperand:
          begin
            if Vdir <> 0 then
              ControlRecord^.Resultado := VEsq / Vdir
            else
            begin
              ControlRecord^.Resultado := 0;
              fIndeterminate := true;
            end;
          end;
        MultiplyMathOperand: ControlRecord^.Resultado := VEsq * Vdir;
        SinMathDegOperand: ControlRecord^.Resultado := VEsq * sin(DegToRad(Vdir));
        CosMathDegOperand: ControlRecord^.Resultado := VEsq * cos(DegToRad(Vdir));
        TanMathDegOperand: ControlRecord^.Resultado := VEsq * tan(DegToRad(Vdir));
        SinMathRadOperand: ControlRecord^.Resultado := VEsq * sin(Vdir);
        CosMathRadOperand: ControlRecord^.Resultado := VEsq * cos(Vdir);
        TanMathRadOperand: ControlRecord^.Resultado := VEsq * tan(Vdir);
        ExpMathOperand: ControlRecord^.Resultado := power(VEsq, Vdir);
      end;
      result := ControlRecord^.Resultado;
    end;
  end
  else
    result := 0
end;

function THTDParser.PrintTree(ControlRecord: PHTDParserNode): string;
var
  r: string;
begin
  if ControlRecord <> nil then
  begin
    r := '';
    case ControlRecord^.ValueType of
      Unknown: r := 'U';
      EmptyCreation: r := 'E';
      DivideMathOperand: r := 'O/';
      MultiplyMathOperand: r := 'O*';
      PlusMathOperand: r := 'O+';
      MinusMathOperand: r := 'O-';
      NumericValue: r := 'N-' + cURRTOsTR(ControlRecord^.NumValue);
      CloseOperand: r := 'Oc';
      OpenOperand: r := 'Oo';
      SinMathDegOperand: r := 'OSD';
      CosMathDegOperand: r := 'OCD';
      TanMathDegOperand: r := 'OTD';
      SinMathRadOperand: r := 'OSR';
      CosMathRadOperand: r := 'OCR';
      TanMathRadOperand: r := 'OTR';
      ExpMathOperand: r := 'O^';
    end;
    r := r + #10#13 + '(' + PrintTree(ControlRecord^.P_FEsq) + ',' + PrintTree(ControlRecord^.P_FDir) + ')';
    result := r;
  end
  else
    result := '';
end;

end.

