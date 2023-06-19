private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy) {
    Deque<CtMethod<?>> stack = new ArrayDeque<>();
    Set<CtMethod<?>> visited = new HashSet<>();

    stack.push(method);

    while (!stack.isEmpty()) {
        CtMethod<?> currentMethod = stack.pop();

        if (!visited.contains(currentMethod)) {
            hierarchy.append(currentMethod.getSignature()).append("\n");
            visited.add(currentMethod);

            CtClass<?> declaringClass = currentMethod.getDeclaringType();
            if (declaringClass != null) {
                Set<CtMethod<?>> methods = declaringClass.getAllMethods();

                for (CtMethod<?> parentMethod : methods) {
                    if (!visited.contains(parentMethod) && parentMethod.getReference().equals(currentMethod.getReference())) {
                        stack.push(parentMethod);
                    }
                }
            }
        }
    }
}
