package calc;

import java.util.*;

import static java.lang.Double.NaN;
import static java.lang.Double.doubleToLongBits;
import static java.lang.Double.valueOf;
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
        List<String> tokens = tokenize(expr);
        List<String> postfix = infix2Postfix(tokens);
        double result = evalPostfix(postfix);
        // TODO List<String> tokens = tokenize(expr);
        // TODO List<String> postfix = infix2Postfix(tokens);
        // TODO double result = evalPostfix(postfix);
        return result; // result;
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
        } else if ("(".contains(op)) {
            return 1;
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

    List<String> tokenize(String input) {
        List<String> tokenized = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        String s1 = new String();
        int count = 1;
        for (char s : removeBlanks(input)) {
            if (count == removeBlanks(input).length) { //todo
                if (Character.isDigit(s) || isOperator(s)) {
                    sb.append(s);
                    s1 = sb.toString();
                    tokenized.add(s1);
                    sb = new StringBuilder();
                }                                           //HÄR ÄR PROBLEMET
                count++;

            } else if (Character.isDigit(s)) {
                sb.append(s);
                count++;
            } else if (isOperator(s)) {
                s1 = sb.toString();
                if (!s1.isEmpty()) {
                    tokenized.add(s1);
                    sb = new StringBuilder();
                }
                sb.append(s);
                s1 = sb.toString();
                tokenized.add(s1);
                sb = new StringBuilder();
                count++;
            }
        }

        return tokenized;

    }

    boolean isOperator(char ch) {
        return "+-*/^()".indexOf(ch) >= 0;
    }

    boolean isParen(char ch) {
        return "()".indexOf(ch) >= 0;
    }

    boolean DoublePower(String operator1, String operator2) {
        if ("^".contains(operator1) && "^".contains(operator2)) {
            return true;
        } else {
            return false;
        }

    }

    boolean isBadOperand(char ch) {
        if (Character.isDigit(ch) || isOperator(ch) || Character.isWhitespace(ch)) {
            return false;
        }
        if (Character.isLetter(ch)) {
            return true;
        } else {
            return true;
        }
    }

    char[] removeBlanks(String input) {
        int index = 0;
        int index2;
        StringBuilder sb = new StringBuilder();
        char[] newCharArr = new char[input.toCharArray().length];
        for (char ch : input.toCharArray()) {
            if (isBadOperand(ch)) {
                throw new RuntimeException(MISSING_OPERAND);
            }
            if (Character.isDigit(ch) || isOperator(ch)) {
                newCharArr[index] = ch;
                index++;
            }
        }
        index2 = index;
        while (index > -1) {
            if (index > 0) {
                sb.append(newCharArr[index2 - index]);
                index--;
            } else {
                break;
            }
        }

        String s = sb.toString();
        char[] trimmed = s.toCharArray();   //right until here then the return of trimmed is wrong
        return trimmed;
    }


    List<String> infix2Postfix(List<String> tokenized) {
        List<String> postfix = new ArrayList<>();
        Deque<String> stack = new ArrayDeque<>();
        for (String s : tokenized) {
            if (isOperator(s.charAt(0))) {
                if (String.valueOf(s).equals("(")) { //problem till senare
                    stack.push(s);
                } else if (String.valueOf(s).equals(")")) {
                    while (true) {
                        if (stack.peek().equals("(")) { // metod för detta
                            stack.pop();
                            break;
                        } else {
                            postfix.add(stack.pop());
                        }
                    }
                } else {
                    if (stack.isEmpty()) {
                        stack.push(s);
                    } else if (getPrecedence(stack.peek()) >= getPrecedence(s) && !DoublePower(stack.peek(), s)) {
                        while (true) {
                            postfix.add(stack.pop());
                            if (stack.isEmpty()) {
                                stack.push(s);
                                break;
                            }
                        }
                    } else {
                        stack.push(s);
                    }
                }
            } else {
                postfix.add(s);
            }
        }
        //final int postfixLength = postfix.size();
        for (String str : stack) {
            if (!stack.isEmpty()) {
                if (!isParen(str.charAt(0))) {
                    postfix.add(stack.pop());
                }
            } else {
                break;
            }
        }
        return postfix;
    }


    double evalPostfix(List<String> postfix) {
        Deque<String> stack = new ArrayDeque<>();
        double result = 0;
        for (String s : postfix) {
            if (isOperator(s.charAt(0))) {
                double stackTop = valueOf(stack.pop());
                double stackUnder = valueOf((stack.pop()));
                result = applyOperator(s, stackTop, stackUnder);
                stack.push(String.valueOf(result));
            } else {
                stack.push(s);
            }
        }
        while (!stack.isEmpty()) {
            result = valueOf(stack.pop());
        }

        return result;
    }


}
