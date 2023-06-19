private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy) {
    CtClass<?> declaringClass = method.getDeclaringType();

    if (declaringClass != null) {
        Set<CtMethod<?>> methods = declaringClass.getAllMethods();
        CtMethod<?>[] methodArray = methods.toArray(new CtMethod<?>[methods.size()]);

        for (CtMethod<?> parentMethod : methodArray) {
            if (parentMethod.getReference().equals(method.getReference())) {
                hierarchy.insert(0, parentMethod.getSignature() + "\n");
                collectHierarchy(parentMethod, hierarchy);
                break;
            }
        }
    }
}
