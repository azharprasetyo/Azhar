1. Pattern (BNF)
<while_stmt> ::= "while" "(" <condition> ")" "{" <statement> "}"

<condition> ::= <identifier> <relop> <number>

<statement> ::= <identifier> "=" <identifier> "+" <number>

<relop> ::= ">" | "<" | ">=" | "<=" | "==" | "!="

<identifier> ::= letter(letter|digit)*

<number> ::= digit+

Grammar tersebut menyatakan bahwa sebuah perulangan terdiri dari keyword while, kondisi, dan satu pernyataan assignment di dalam blok.

2. Implementasi Program (Python)
import 

class WhileCompiler:

    def __init__(self, source_code, symbol_table):
        self.source = source_code
        self.symbol_table = symbol_table
        self.label = 1

    # ==========================
    # LEXICAL ANALYSIS
    # ==========================
    def lexical_analysis(self):

        pattern = r'while|>=|<=|==|!=|>|<|=|\+|\{|\}|\(|\)|[a-zA-Z_]\w*|\d+'

        tokens = re.findall(pattern, self.source)

        return tokens

    # ==========================
    # SYNTAX ANALYSIS
    # ==========================
    def syntax_analysis(self, tokens):

        if tokens[0] != "while":
            raise SyntaxError("Keyword while tidak ditemukan")

        left = tokens[2]
        op = tokens[3]
        right = tokens[4]

        var = tokens[7]
        expr1 = tokens[9]
        expr2 = tokens[11]

        ast = {
            "condition": (left, op, right),
            "statement": (var, expr1, "+", expr2)
        }

        return ast

    # ==========================
    # SEMANTIC ANALYSIS
    # ==========================
    def semantic_analysis(self, ast):

        left = ast["condition"][0]
        stmt = ast["statement"][1]

        if left not in self.symbol_table:
            raise Exception(f"Semantic Error: Variabel {left} belum dideklarasikan.")

        if stmt not in self.symbol_table:
            raise Exception(f"Semantic Error: Variabel {stmt} belum dideklarasikan.")

        print("Semantic Analysis : Berhasil")

    # ==========================
    # TAC GENERATOR
    # ==========================
    def generate_TAC(self, ast):

        start = f"L{self.label}"
        self.label += 1

        end = f"L{self.label}"
        self.label += 1

        cond = ast["condition"]
        stmt = ast["statement"]

        print("\n===== THREE ADDRESS CODE =====\n")

        print(f"{start}:")
        print(f"ifFalse {cond[0]} {cond[1]} {cond[2]} goto {end}")
        print(f"{stmt[0]} = {stmt[1]} + {stmt[3]}")
        print(f"goto {start}")
        print(f"{end}:")
        print("END")


# ==========================
# PROGRAM UTAMA
# ==========================

source = "while ( x < 5 ) { x = x + 1 }"

symbol_table = {
    "x":0
}

compiler = WhileCompiler(source, symbol_table)

tokens = compiler.lexical_analysis()

print("===== TOKEN =====")
print(tokens)

ast = compiler.syntax_analysis(tokens)

print("\n===== AST =====")
print(ast)

compiler.semantic_analysis(ast)

compiler.generate_TAC(ast)
3. Output Program
===== TOKEN =====

['while', '(', 'x', '<', '5', ')', '{', 'x', '=', 'x', '+', '1', '}']

===== AST =====

{
 'condition': ('x','<','5'),
 'statement': ('x','x','+','1')
}

Semantic Analysis : Berhasil

===== THREE ADDRESS CODE =====

L1:
ifFalse x < 5 goto L2
x = x + 1
goto L1
L2:
END
