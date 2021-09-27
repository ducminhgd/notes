# Apdex

Apdex (Application Performance Index) is an open standard developed by an alliance of companies that defines a standardized method to [[report]], [[benchmark]], and track application [[performance]].

Apdex is a numerical measure of user satisfaction with the performance of enterprise applications. It converts many measurements into one number on a uniform scale of 0-to-1 (0 = no users satisfied, 1 = all users satisfied). This metric can be applied to any source of end-user performance measurements

The index is based on three zones of application responsiveness:
- **Satisfied**:  The user is fully productive. This represents the time value (T seconds) below which users are not impeded by application response time.
- **Tolerating**:  The user notices performance lagging within responses greater than T, but continues the process.
- **Frustrated**:  Performance with a response time greater than F seconds is unacceptable, and users may abandon the process.

Formula:

![](https://www.apdex.org/images/overview_formula1_241_75.gif)