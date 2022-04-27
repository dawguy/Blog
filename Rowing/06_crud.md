# CRUD Opeartions
Overall these went way smoother than I originally expected.

Essentially I did the workout controller endpoints that I had originally written and they were doable without much issue.

Then I wanted to create athletes, so I created a basic controller for GET and CREATE operations for athletes. Took a little bit of tweaking, but this was pretty straight forward after getting past the fact that I needed the JsonIgnoreProperties annotation.

```
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @JsonIgnoreProperties(ignoreUnknown = true)
  @Column(name = "athlete")
  private Long athlete;
```

# Generating The Rest
I turned my initial Athlete model into an IntelliJ template. This allowed me to almost immediately create the rest of controllers with only a couple clicks.

One thing to note is that I didn't dig deep enough to learn how to lowercase terms (or add variables). So I relied on lowercase prefixed versions of the template having AA prefixed before them. That way I could do something like this global replace `:%s/g/AA([A-Z])(\w+)/\l$1$2`. And take care of all lowercasing letters instantly.

# Templates have a lot of power from what I've seen.
