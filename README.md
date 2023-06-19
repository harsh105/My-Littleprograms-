private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy) {
    CtClass<?> declaringClass = method.getDeclaringType();

    if (declaringClass != null) {
        CtMethod<?> parentMethod = findParentMethod(declaringClass, method);
        if (parentMethod != null) {
            collectHierarchy(parentMethod, hierarchy);
        }
    }

    hierarchy.append(method.getSignature()).append("\n");
}

private CtMethod<?> findParentMethod(CtClass<?> declaringClass, CtMethod<?> method) {
    CtMethod<?>[] methods = declaringClass.getAllMethods();

    for (CtMethod<?> parentMethod : methods) {
        if (parentMethod.getReference().equals(method.getReference())) {
            return parentMethod;
        }
    }

    return null;
}
