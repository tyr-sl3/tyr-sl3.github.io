
```ml
type Operand =
  | Add
  | Sub
  | Mul
  | Div
  | Mod
  ;;

type Expression =
  | Literal of int
  | Operation of Expression * Operand * Expression
  ;;

type Statement =
  | Assignment of string * Expression
  | Sequence of Statement * Statement
  ;;

let rec evalExpr = function
  | Literal literal_val  -> literal_val
  | Operation(e1,Add,e2) -> (evalExpr e1) + (evalExpr e2)
  | Operation(e1,Sub,e2) -> (evalExpr e1) - (evalExpr e2)
  | Operation(e1,Mul,e2) -> (evalExpr e1) * (evalExpr e2)
  | Operation(e1,Div,e2) -> (evalExpr e1) / (evalExpr e2)
  | Operation(e1,Mod,e2) -> (evalExpr e1) % (evalExpr e2)
  ;;
  
let assignment =
  Assignment("result", Operation(Literal 42, Add, Literal 1));;

printfn "%d" (evalExpr (Operation(Literal 42, Add, Literal 1)));;
```