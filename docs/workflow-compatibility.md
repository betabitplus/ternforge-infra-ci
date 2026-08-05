# Workflow compatibility

Released reusable workflow interfaces are consumed by exact commit SHA with a
human-readable tag comment. A breaking interface change requires a new release;
existing consumers remain reproducible until their caller pin is deliberately
updated through review.
