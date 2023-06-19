private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy) {
    Queue<CtMethod<?>> queue = new LinkedList<>();
    Set<CtMethod<?>> visited = new HashSet<>();

    queue.add(method);
    visited.add(method);

    while (!queue.isEmpty()) {
        CtMethod<?> currentMethod = queue.poll();
        hierarchy.append(currentMethod.getSignature()).append("\n");

        CtClass<?> declaringClass = currentMethod.getDeclaringType();
        if (declaringClass != null) {
            Set<CtMethod<?>> methods = declaringClass.getAllMethods();

            for (CtMethod<?> parentMethod : methods) {
                if (!visited.contains(parentMethod) && parentMethod.getReference().equals(currentMethod.getReference())) {
                    queue.add(parentMethod);
                    visited.add(parentMethod);
                }
            }
        }
    }
}
