#readme
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import spoon.Launcher;
import spoon.reflect.CtModel;
import spoon.reflect.declaration.CtMethod;
import spoon.reflect.declaration.CtType;

import java.util.ArrayList;
import java.util.List;

@RestController
public class MethodHierarchyController {

    @GetMapping("/method-hierarchy")
    public String getMethodHierarchy(@RequestParam String methodName, @RequestParam String className) {
        Launcher launcher = new Launcher();
        launcher.addInputResource("src/main/java"); // Set the path to your source code directory

        launcher.buildModel();
        CtModel model = launcher.getModel();

        CtMethod<?> method = model.getElements(new NameFilter<>(methodName)).stream()
                .filter(e -> e instanceof CtMethod)
                .map(e -> (CtMethod<?>) e)
                .findFirst()
                .orElse(null);

        if (method != null) {
            StringBuilder hierarchy = new StringBuilder();
            collectHierarchy(method, hierarchy, "");
            return hierarchy.toString();
        } else {
            return "Method not found.";
        }
    }

    private void collectHierarchy(CtMethod<?> method, StringBuilder hierarchy, String indent) {
        hierarchy.append(indent).append(method.getSimpleName()).append("\n");

        CtType<?> declaringType = method.getDeclaringType();
        if (declaringType != null) {
            List<CtMethod<?>> parentMethods = new ArrayList<>();
            parentMethods.addAll(declaringType.getAllMethods());
            parentMethods.stream()
                    .filter(parentMethod -> parentMethod.getReference().overrides(method.getReference()))
                    .forEach(parentMethod -> collectHierarchy(parentMethod, hierarchy, indent + "\t"));
        }
    }
}
