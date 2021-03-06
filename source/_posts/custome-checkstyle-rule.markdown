---
layout: post
title: "自定义CheckStyle规则"
date: 2013-09-25 13:11
comments: true
categories: 代码规范
tags: [ checkstyle, antlr, ast ]
---
### CheckStyle基于antlr对源码进行处理

- antlr对AST解析
- 使用Visitor模式

主要是通过：

```java
public int[] getDefaultTokens()
```

指定要访问的节点类型。

```java
public void visitToken(DetailAST assignAST)
```

指定如何处理节点，并进行规则校验。

<!--more-->
#### 参数不可在方法内重新赋值

```java
import com.puppycrawl.tools.checkstyle.api.Check;
import com.puppycrawl.tools.checkstyle.api.DetailAST;
import com.puppycrawl.tools.checkstyle.api.TokenTypes;

import java.util.ArrayList;
import java.util.List;

/**
 * Date:  13-8-26
 * Time:  下午4:34
 * checks the Method Parameter should not assign.
 *
 * @author shenyanchao
 */
public class ParameterNoAssignCheck extends Check {

    private int[] assignTokenTypes = {
            TokenTypes.ASSIGN,
            TokenTypes.PLUS_ASSIGN,
            TokenTypes.MINUS_ASSIGN,
            TokenTypes.STAR_ASSIGN,
            TokenTypes.DIV_ASSIGN,
            TokenTypes.MOD_ASSIGN,
            TokenTypes.SR_ASSIGN,
            TokenTypes.BSR_ASSIGN,
            TokenTypes.SL_ASSIGN,
            TokenTypes.BAND_ASSIGN,
            TokenTypes.BXOR_ASSIGN,
            TokenTypes.BOR_ASSIGN
    };

    @Override
    public int[] getDefaultTokens() {
        return assignTokenTypes;
    }
    
    @Override
    public void visitToken(DetailAST assignAST) {
        if (null == assignAST) {
            return;
        }
        DetailAST leftVarAST = assignAST.findFirstToken(TokenTypes.IDENT);
        if (null == leftVarAST) {
            return;
        }
        String leftVar = leftVarAST.getText();
        DetailAST methodDefAST = findParentMethodDefBy(assignAST);
        if (null != methodDefAST) {
            List<String> parameters = findMethodParameterNames(methodDefAST);
            if (parameters.contains(leftVar)) {
                log(leftVarAST.getLineNo(), "Method parameter [" + leftVar + "] should not assign!");
            }
        }
    }
    private List<String> findMethodParameterNames(DetailAST methodDefAST) {
        List<String> parameters = new ArrayList<String>();
        if (null != methodDefAST) {
            DetailAST parametersAST = methodDefAST.findFirstToken(TokenTypes.PARAMETERS);
            if (null != parametersAST) {
                DetailAST parameterDefAST = parametersAST.getFirstChild();
                while (null != parameterDefAST) {
                    if (parameterDefAST.getType() == TokenTypes.PARAMETER_DEF) {
                        String parameterName = parameterDefAST.findFirstToken(TokenTypes.IDENT).getText();
                        parameters.add(parameterName);
                    }
                    parameterDefAST = parameterDefAST.getNextSibling();
                }
            }
        }
        return parameters;
    }

    /**
     * @param aAST aAST
     * @return ancestor METHOD_DEF or null
     */
    private DetailAST findParentMethodDefBy(DetailAST aAST) {
        if (null == aAST || aAST.getType() == TokenTypes.METHOD_DEF) {
            return aAST;
        } else {
            return findParentMethodDefBy(aAST.getParent());
        }
    }

}
```

#### 控制使用String连+的数量


```java
import com.puppycrawl.tools.checkstyle.api.Check;
import com.puppycrawl.tools.checkstyle.api.DetailAST;
import com.puppycrawl.tools.checkstyle.api.TokenTypes;

/**
 * Date:  13-8-26
 * Time:  下午1:29
 *
 * @author shenyanchao
 */
public class ConcatStringCheck extends Check {

    private static final int DEFAULT_MAX = 10;
    private int max = DEFAULT_MAX;

    @Override
    public int[] getDefaultTokens() {
        return new int[]{TokenTypes.EXPR};
    }

    @Override
    public void visitToken(DetailAST ast) {
        int plusCount = findAllSubNodeIn(ast, TokenTypes.PLUS);
        if (plusCount > max - 1) {
            log(ast.getLineNo(), "more than " + (max) + " string concat,please use StringBuffer or StringBuilder " +
                    "instead");
        }
    }
    public void setMax(int limit) {
        max = limit;
    }

    private int findAllSubNodeIn(DetailAST ast, int tokenTypes) {
        if (ast.getChildCount() == 0) {
            return 0;
        } else {
            int count = 0;
            count += ast.getChildCount(tokenTypes);
            DetailAST childAST = ast.getFirstChild();
            while (null != childAST) {
                count += findAllSubNodeIn(childAST, tokenTypes);
                childAST = childAST.getNextSibling();
            }
            return count;
        }
    }

}
```
