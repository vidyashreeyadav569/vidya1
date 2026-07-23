import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

public class p2 {

    static class Resume {
        String name;
        int yearsOfExperience;
        List<String> skills;
        String education;

        Resume(String name, int yearsOfExperience, List<String> skills, String education) {
            this.name = name;
            this.yearsOfExperience = yearsOfExperience;
            this.skills = skills;
            this.education = education;
        }
    }

    static class JobPosting {
        String title;
        int minExperience;
        List<String> requiredSkills;
        List<String> preferredEducation;

        JobPosting(String title, int minExperience, List<String> requiredSkills, List<String> preferredEducation) {
            this.title = title;
            this.minExperience = minExperience;
            this.requiredSkills = requiredSkills;
            this.preferredEducation = preferredEducation;
        }
    }

    static class ScreeningResult {
        Resume resume;
        int score;
        boolean shortlisted;
        List<String> reasons;

        ScreeningResult(Resume resume, int score, boolean shortlisted, List<String> reasons) {
            this.resume = resume;
            this.score = score;
            this.shortlisted = shortlisted;
            this.reasons = reasons;
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        JobPosting job = new JobPosting(
                "Java Developer",
                3,
                Arrays.asList("Java", "OOP", "Data Structures", "SQL"),
                Arrays.asList("Bachelor", "Master")
        );

        System.out.println("AI Based Resume Screening Job Portal");
        System.out.println("Job: " + job.title);
        System.out.println("Minimum Experience: " + job.minExperience + " years");
        System.out.println("Required Skills: " + job.requiredSkills);
        System.out.println("Preferred Education: " + job.preferredEducation);
        System.out.println();

        List<Resume> resumes = new ArrayList<>();
        resumes.add(new Resume("Ravi Kumar", 4, Arrays.asList("Java", "Spring", "SQL", "OOP"), "Bachelor"));
        resumes.add(new Resume("Meera Sharma", 2, Arrays.asList("Java", "OOP", "HTML"), "Bachelor"));
        resumes.add(new Resume("Amit Singh", 5, Arrays.asList("Java", "Data Structures", "SQL", "Microservices"), "Master"));

        for (Resume resume : resumes) {
            ScreeningResult result = screenResume(resume, job);
            showResult(result);
            System.out.println();
        }

        System.out.println("Enter new resume data to screen manually:");
        System.out.print("Name: ");
        String name = scanner.nextLine().trim();
        System.out.print("Years of experience: ");
        int years = Integer.parseInt(scanner.nextLine().trim());
        System.out.print("Skills (comma separated): ");
        List<String> skills = parseList(scanner.nextLine());
        System.out.print("Education: ");
        String education = scanner.nextLine().trim();

        Resume customResume = new Resume(name, years, skills, education);
        ScreeningResult customResult = screenResume(customResume, job);
        showResult(customResult);
        scanner.close();
    }

    private static ScreeningResult screenResume(Resume resume, JobPosting job) {
        int score = 0;
        List<String> reasons = new ArrayList<>();

        if (resume.yearsOfExperience >= job.minExperience) {
            score += 30;
        } else {
            reasons.add("Insufficient experience: " + resume.yearsOfExperience + " years.");
        }

        int skillMatches = 0;
        for (String required : job.requiredSkills) {
            for (String actual : resume.skills) {
                if (actual.equalsIgnoreCase(required)) {
                    skillMatches++;
                    break;
                }
            }
        }
        score += skillMatches * 15;
        if (skillMatches < job.requiredSkills.size()) {
            reasons.add("Missing required skills.");
        }

        boolean educationMatch = false;
        for (String preferred : job.preferredEducation) {
            if (resume.education.equalsIgnoreCase(preferred)) {
                educationMatch = true;
                break;
            }
        }
        if (educationMatch) {
            score += 10;
        } else {
            reasons.add("Preferred education not met.");
        }

        boolean shortlisted = score >= 60;
        if (shortlisted) {
            reasons.add("Resume shortlisted with score " + score + ".");
        } else {
            reasons.add("Resume not shortlisted. Score " + score + ".");
        }

        return new ScreeningResult(resume, score, shortlisted, reasons);
    }

    private static List<String> parseList(String input) {
        List<String> values = new ArrayList<>();
        for (String item : input.split(",")) {
            String trimmed = item.trim();
            if (!trimmed.isEmpty()) {
                values.add(trimmed);
            }
        }
        return values;
    }

    private static void showResult(ScreeningResult result) {
        System.out.println("Candidate: " + result.resume.name);
        System.out.println("Experience: " + result.resume.yearsOfExperience + " years");
        System.out.println("Skills: " + result.resume.skills);
        System.out.println("Education: " + result.resume.education);
        System.out.println("Score: " + result.score);
        System.out.println("Shortlisted: " + (result.shortlisted ? "Yes" : "No"));
        System.out.println("Reasons:");
        for (String reason : result.reasons) {
            System.out.println(" - " + reason);
        }
    }
}
