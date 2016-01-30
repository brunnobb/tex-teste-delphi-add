unit Main;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, StdCtrls, ExtCtrls, Buttons, Math, DB, DBClient;

type
  TForm1 = class(TForm)
    Panel1: TPanel;
    Label5: TLabel;
    Panel2: TPanel;
    Label6: TLabel;
    Edit3: TEdit;
    JvIntegerEdit1: TEdit;
    Label8: TLabel;
    JvIntegerEdit2: TEdit;
    Label9: TLabel;
    Label10: TLabel;
    Button3: TButton;
    Panel5: TPanel;
    Button1: TButton;
    pnlManual: TPanel;
    Label1: TLabel;
    Label2: TLabel;
    Label3: TLabel;
    Label4: TLabel;
    Edit1: TEdit;
    Button14: TButton;
    Edit2: TEdit;
    Edit4: TEdit;
    JvFloatEdit1: TEdit;
    Panel3: TPanel;
    Button2: TButton;
    pnlAjuda: TPanel;
    Label7: TLabel;
    Label11: TLabel;
    Label12: TLabel;
    Label13: TLabel;
    Label14: TLabel;
    Label15: TLabel;
    Label16: TLabel;
    Label17: TLabel;
    Label18: TLabel;
    Label19: TLabel;
    Label20: TLabel;
    Panel4: TPanel;
    Edit5: TEdit;
    Label21: TLabel;
    Label22: TLabel;
    Label23: TLabel;
    Label24: TLabel;
    Shape2: TShape;
    Shape3: TShape;
    Label28: TLabel;
    Label29: TLabel;
    Image1: TImage;
    Memo1: TMemo;
    Button4: TButton;
    procedure Button14Click(Sender: TObject);
    procedure Panel3Click(Sender: TObject);
    procedure Button1Click(Sender: TObject);
    procedure Button2Click(Sender: TObject);
    procedure Panel4Click(Sender: TObject);
    procedure Button3Click(Sender: TObject);
    procedure GenerateValues;
    procedure DrawGraph(cds: TClientDataSet);
    procedure Button4Click(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

uses HTDPArserMath;

{$R *.dfm}

procedure TForm1.Button14Click(Sender: TObject);
var
  HTDParser1: THTDParser;
begin
  try
    HTDParser1 := THTDParser.Create;
    HTDParser1.Texto := Edit1.text;
    HTDParser1.AddNewVariable('X', StrToInt(JvFloatEdit1.Text));
    HTDParser1.AddNewVariable('x', StrToInt(JvFloatEdit1.Text));
    HTDParser1.Calculate;

    edit4.Text := FormatFloat('0.00000000', HTDParser1.Answer);
    if HTDParser1.indeterminate then
      edit4.Text := 'indeterminate';
    edit2.Text := HTDParser1.Texto;

  except
    on e: exception do
      showmessage('Erro ao calcular a Ficha' + #10#13 + e.Message)
  end;
  FreeAndNil(HTDParser1);
end;

procedure TForm1.Panel3Click(Sender: TObject);
begin
  pnlManual.Visible := false;
end;

procedure TForm1.Button1Click(Sender: TObject);
begin
  pnlManual.Visible := true;
end;

procedure TForm1.Button2Click(Sender: TObject);
begin
  pnlAjuda.Visible := true;
end;

procedure TForm1.Panel4Click(Sender: TObject);
begin
  pnlAjuda.Visible := false;
end;

procedure TForm1.Button3Click(Sender: TObject);
begin

  Image1.Canvas.Brush.Color := ClWhite;
  Image1.Canvas.FillRect(Image1.Canvas.ClipRect);
  Image1.Canvas.Refresh;
  Label21.Caption := JvIntegerEdit1.Text;
  Label22.Caption := JvIntegerEdit2.Text;
  label29.Caption := formatfloat('0.00', (StrToInt(JvIntegerEdit1.Text) + StrToInt(JvIntegerEdit2.Text) / 2);
  GenerateValues;
end;

procedure TForm1.GenerateValues;
var
  cdsVal: TClientDataset;
  i: integer;
  HTDParser1: THTDParser;
  ini, fim, vx, vy, y, py, passo, pos, max, min: Extended;
  ind: boolean;
  pmax, pmin: integer;

begin

  cdsVal := TClientDataSet.Create(nil);
  cdsVal.FieldDefs.Add('X', ftFloat);
  cdsVal.FieldDefs.Add('VX', ftFloat);
  cdsVal.FieldDefs.Add('VY', ftFloat);
  cdsVal.FieldDefs.Add('Y', ftFloat);
  cdsVal.FieldDefs.Add('IND', ftBoolean);
  cdsVal.CreateDataSet;
  for i := 0 to 350 do
  begin
    try
      ini := JvIntegerEdit1.Value;
      fim := JvIntegerEdit2.Value;
      passo := ((fim - ini) / 350);
      vx := ini + (passo * i);
      HTDParser1 := THTDParser.Create;
      HTDParser1.Texto := Edit3.text;
      HTDParser1.AddNewVariable('X', vx);
      HTDParser1.AddNewVariable('x', vx);
      HTDParser1.Calculate;

      vy := HTDParser1.Answer;
      ind := false;
      if HTDParser1.indeterminate then
        ind := true;

      cdsVAl.Insert;
      cdsVal.FieldByName('X').AsFloat := i;
      cdsVal.FieldByName('VX').AsFloat := vx;
      cdsVal.FieldByName('VY').AsFloat := vy;
      cdsVal.FieldByName('IND').AsBoolean := ind;
      cdsVal.Post;
    except
      on e: exception do
      begin
        showmessage('Erro ao calcular a Ficha' + #10#13 + e.Message);
        FreeAndNil(HTDParser1);
        abort;
      end;
    end;
    FreeAndNil(HTDParser1);
  end;

  cdsVal.First;

  min := cdsVal.FieldByName('VY').AsFloat;
  max := cdsVal.FieldByName('VY').AsFloat;
  while not cdsVal.Eof do
  begin
    if cdsVal.FieldByName('VY').AsFloat < min then
      min := cdsVal.FieldByName('VY').AsFloat;
    if cdsVal.FieldByName('VY').AsFloat > max then
      max := cdsVal.FieldByName('VY').AsFloat;
    cdsVal.Next;

    pmax := round(max);
    pmin := round(min);
  end;

  if pmax > pmin * (-1) then
    pmin := pmax * (-1);

  if pmin * (-1) > pmax then
    pmax := pmin * (-1);


  if pmin=0 then
    pmin:=-1;
  if pmax=0 then
    pmax:=1;
  Label23.Caption := IntToStr(pmin);
  Label24.Caption := IntToStr(pmax);
  label28.Caption := formatfloat('0.00', ((pmax + pmin) / 2));

  cdsVal.First;
  while not cdsVal.Eof do
  begin
    ini := pmin;
    fim := pmax;


    passo := (175 / fim);

    cdsVal.edit;
    cdsVal.FieldByName('x').AsInteger;
    cdsVal.FieldByName('y').AsInteger := 175 - Round((cdsVal.FieldByName('vy').AsFloat * passo));
    
    cdsVal.Post;
    cdsVal.Next;
  end;
  DrawGraph(cdsVal);
end;

procedure TForm1.DrawGraph(cds: TClientDataSet);
var
  ox, oy: integer;
  procedure Draw(Canvas: TCanvas; x: integer; y: integer);
  begin
    with Canvas do
    begin
      pen.Color := clRed;
      MoveTo(ox, oy);
      LineTo(x, y);
      memo1.Lines.Add('x= ' + cds.fieldbyname('vx').AsString + '/' + cds.fieldbyname('x').AsString);
      memo1.Lines.Add('y= ' + cds.fieldbyname('vy').AsString + '/' + cds.fieldbyname('y').AsString);
      memo1.Lines.Add('');
    end;
  end;

  procedure InverteBase;
  begin
    cds.Edit;
    cds.fieldbyname('y').AsInteger := ((cds.fieldbyname('y').AsInteger - 350) * (-1));
    cds.Post;
  end;
begin
  cds.First;
  memo1.lines.Clear;
  {while not cds.Eof do
  begin
    InverteBase;
    cds.Next;
  end;}
  cds.AddIndex('indice', 'X', []);
  cds.IndexName := 'indice';
  cds.First;
  ox := cds.fieldbyname('x').AsInteger;
  oy := cds.fieldbyname('y').AsInteger;
  while not cds.Eof do
  begin
    if not cds.FieldByName('IND').AsBoolean then
    begin
      Draw(Image1.Canvas, cds.fieldbyname('x').AsInteger, cds.fieldbyname('y').AsInteger);
      ox := cds.fieldbyname('x').AsInteger;
      oy := cds.fieldbyname('y').AsInteger;
    end;
    cds.Next;
  end;
end;

procedure TForm1.Button4Click(Sender: TObject);
begin
  memo1.Visible := true;
  memo1.BringToFront;
end;

end.

