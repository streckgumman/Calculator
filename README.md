package calc;

import java.util.*;

import static java.lang.Double.NaN;
import static java.lang.Math.pow;


/*
 *   A calculator for rather simple arithmetic expressions
 *
 *   This is not the program, it's a class declaration (with methods) in it's
 *   own file (which must be named Calculator.java)
 *
 *   NOTE:
 *   - No negative numbers implemented
 */
class Calculator {

    // Here are the only allowed instance variables!
    // Error messages (more on static later)
    final static String MISSING_OPERAND = "Missing or bad operand";
    final static String DIV_BY_ZERO = "Division with 0";
    final static String MISSING_OPERATOR = "Missing operator or parenthesis";
    final static String OP_NOT_FOUND = "Operator not found";

    // Definition of operators
    final static String OPERATORS = "+-*/^";

    // Method used in REPL
    double eval(String expr) {
        if (expr.length() == 0) {
            return NaN;
        }
        // TODO List<String> tokens = tokenize(expr);
        // TODO List<String> postfix = infix2Postfix(tokens);
        // TODO double result = evalPostfix(postfix);
        return 0; // result;
    }

    // ------  Evaluate RPN expression -------------------

    // TODO Eval methods

    double applyOperator(String op, double d1, double d2) {
        switch (op) {
            case "+":
                return d1 + d2;
            case "-":
                return d2 - d1;
            case "*":
                return d1 * d2;
            case "/":
                if (d1 == 0) {
                    throw new IllegalArgumentException(DIV_BY_ZERO);
                }
                return d2 / d1;
            case "^":
                return pow(d2, d1);
        }
        throw new RuntimeException(OP_NOT_FOUND);
    }

    // ------- Infix 2 Postfix ------------------------

    // TODO Methods








    int getPrecedence(String op) {
        if ("+-".contains(op)) {
            return 2;
        } else if ("*/".contains(op)) {
            return 3;
        } else if ("^".contains(op)) {
            return 4;
        } else {
            throw new RuntimeException(OP_NOT_FOUND);
        }
    }

    enum Assoc {
        LEFT,
        RIGHT
    }

    Assoc getAssociativity(String op) {
        if ("+-*/".contains(op)) {
            return Assoc.LEFT;
        } else if ("^".contains(op)) {
            return Assoc.RIGHT;
        } else {
            throw new RuntimeException(OP_NOT_FOUND);
        }
    }


    // ---------- Tokenize -----------------------

    // TODO Methods to tokenize

    /*List <String> tokenize(String input){
        StringBuilder sb = new StringBuilder();
        Deque<Character> stack = new ArrayDeque<>();
        //int pushed = 0;
        for (char s : input.toCharArray()){
            if (Character.isDigit(s)){
                stack.push(s);
                //pushed++;
            }
            else if (isOperator(s)){
                if (!stack.isEmpty())
                sb.append(stack.pop());
                sb.append(s);
                stack.clear();
            }
        }


    return null;
    }*/

    List <String> tokenize2(String input){
        List<String> tokenized = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        String s1 = new String();
        int count = 1;
        for (char s : removeBlanks(input)){
            if (count == input.toCharArray().length){ //todo
                sb.append(s);
                s1 = sb.toString();
                tokenized.add(s1);
                sb =new StringBuilder();
                count++;
            }
            else if (Character.isDigit(s)){
                sb.append(s);
                count++;
            }
            else if (isOperator(s)){
                s1 = sb.toString();
                tokenized.add(s1);
                sb = new StringBuilder();
                sb.append(s);
                s1 = sb.toString();
                tokenized.add(s1);
                sb =new StringBuilder();
                count++;
            }
        }

    return tokenized;

    }
    boolean isOperator(char ch) {
        return "+-*/^()".indexOf(ch) >= 0;
    }
    char [] removeBlanks(String input){
        int index = 0;
        char[] newCharArr = new char[input.toCharArray().length + ((input.toCharArray().length)- index)];
        for (char ch : input.toCharArray()){
            if (Character.isDigit(ch) || isOperator(ch)){
                newCharArr[index] = ch;
                index++; //todo
            }
        }
        return newCharArr;
    }



}
