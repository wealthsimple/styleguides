# Objective-C Style Guide

This is Wealthsimple's Objective-C & iOS Style Guide.

# Documentation and Organization

- All method declarations should be self documented by the name.
- All methods should aim to do one thing
- Use #pragma mark's to categorize methods into functional groups, protocol implementations and visibility.
- Follow the project file structure and ensure that the local structure matches the xcode structure. Currently they are stuctured by class type and typically prefixed with "WS".

# Testing

- We add bdd style tests through Cedar, https://github.com/pivotal/cedar.

# Dependency injection

- We use dependency injection through Blindside, https://github.com/jbsf/blindside.

# DRY - Don't repeat yourself

- When possible, refactor common functionality into -Utils, or +Categories. This allows code to be more readable and allows us to develop future features faster.

# UI

- The majority of visual elements and settings should be within the interface builder, within Storyboards (or Xibs)
- Currently we have many view controllers in the Main.Storyboard, we have a long term goal of breaking this down into smaller more manageable storyboards.
- Use Storyboard references to connect storyboards together.

# Code smells (Something is probably wrong when...)

- If you are modifying the frame of a view
- When a view controller has too many lines of code


 


