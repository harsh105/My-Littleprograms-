import org.eclipse.jdt.core.*;
import org.eclipse.jdt.core.dom.*;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

@RestController
public class MethodHierarchyController {

    @GetMapping("/methodHierarchy")
    public List<String> getMethodHierarchy(
            @RequestParam("methodName") String methodName,
            @RequestParam("className") String className
    ) {
        List<String> methodHierarchy = new ArrayList<>();
        try {
            IJavaProject javaProject = findJavaProject();
            if (javaProject != null) {
                IType type = javaProject.findType(className);
                if (type != null) {
                    ICompilationUnit compilationUnit = type.getCompilationUnit();
                    if (compilationUnit != null) {
                        ASTParser parser = ASTParser.newParser(ASTParser.K_COMPILATION_UNIT);
                        parser.setKind(ASTParser.K_COMPILATION_UNIT);
                        parser.setSource(compilationUnit);
                        parser.setResolveBindings(true);
                        parser.setBindingsRecovery(true);
                        parser.setEnvironment(
                                new String[]{},
                                new String[]{},
                                new String[]{},
                                true
                        );

                        CompilationUnit astRoot = (CompilationUnit) parser.createAST(null);
                        astRoot.accept(new ASTVisitor() {
                            @Override
                            public boolean visit(MethodInvocation node) {
                                IMethodBinding binding = node.resolveMethodBinding();
                                if (binding != null && binding.getName().equals(methodName)) {
                                    methodHierarchy.add(binding.getDeclaringClass().getQualifiedName());
                                }
                                return super.visit(node);
                            }
                        });
                    }
                }
            }
        } catch (JavaModelException e) {
            e.printStackTrace();
        }
        return methodHierarchy;
    }

    private IJavaProject findJavaProject() throws JavaModelException {
        IJavaProject[] projects = JavaCore.create(ResourcesPlugin.getWorkspace().getRoot())
                .getJavaProjects();
        for (IJavaProject project : projects) {
            if (project.isOnClasspath(new Path(getApplicationClasspath()))) {
                return project;
           
