private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy) {
    CtClass<?> declaringClass = method.getDeclaringType();

    if (declaringClass != null) {
        Set<CtMethod<?>> methods = declaringClass.getAllMethods();
        CtMethod<?>[] methodArray = methods.toArray(new CtMethod<?>[methods.size()]);

        CtMethod<?> parentMethod = findParentMethod(methodArray, method);
        if (parentMethod != null) {
            collectHierarchy(parentMethod, hierarchy);
        }
    }

    hierarchy.append(method.getSignature()).append("\n");
}

private CtMethod<?> findParentMethod(CtMethod<?>[] methods, CtMethod<?> method) {
    for (CtMethod<?> parentMethod : methods) {
        if (parentMethod.getReference().equals(method.getReference())) {
            return parentMethod;
        }
    }

    return null;
}
