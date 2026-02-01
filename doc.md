src/
├── animations/
│   ├── pageTransitions.ts
│   ├── transitions.ts
│   └── variants.ts
├── app/
│   ├── guards/
│   │   ├── AuthGuard.tsx
│   │   └── RoleGuard.tsx
│   ├── providers/
│   │   ├── AuthProvider.tsx
│   │   ├── QueryProvider.tsx
│   │   └── ThemeProvider.tsx
│   └── ErrorBoundary.tsx
├── assets/
│   ├── fonts/
│   ├── icons/
│   ├── illustrations/
│   ├── images/
│   ├── lotties/
│   └── react.svg
├── components/
│   ├── data/
│   │   ├── DataTable.tsx
│   │   └── Pagination.tsx
│   ├── feedback/
│   │   ├── Alert.tsx
│   │   └── Toast.tsx
│   ├── ui/
│   │   ├── Badge.tsx
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Modal.tsx
│   │   ├── Select.tsx
│   │   └── Skeleton.tsx
│   └── index.ts
├── config/
│   ├── constants.ts
│   ├── env.ts
│   └── roles.ts
├── features/
│   ├── attendance/
│   │   ├── components/
│   │   │   ├── AttendanceFilter.tsx
│   │   │   └── AttendanceTable.tsx
│   │   ├── api.ts
│   │   ├── hooks.ts
│   │   ├── types.ts
│   │   └── utils.ts
│   ├── auth/
│   │   ├── api.ts
│   │   ├── hooks.ts
│   │   └── types.ts
│   ├── courses/
│   ├── fees/
│   ├── grades/
│   └── users/
├── hooks/
│   ├── useDebounce.ts
│   ├── useKeyboard.ts
│   └── useMediaQuery.ts
├── layouts/
│   ├── AuthLayout.tsx
│   ├── DashboardLayout.tsx
│   └── PublicLayout.tsx
├── pages/
│   ├── admin/
│   │   ├── Dashboard.tsx
│   │   └── Users.tsx
│   ├── faculty/
│   │   ├── Attendance.tsx
│   │   └── Dashboard.tsx
│   ├── student/
│   │   ├── Attendance.tsx
│   │   ├── Dashboard.tsx
│   │   └── Grades.tsx
│   └── NotFound.tsx
├── routes/
│   ├── admin.routes.tsx
│   ├── faculty.routes.tsx
│   ├── index.tsx
│   └── student.routes.tsx
├── services/
│   ├── analytics.ts
│   ├── http.ts
│   └── storage.ts
├── store/
│   ├── auth.store.ts
│   ├── index.ts
│   └── ui.store.ts
├── styles/
│   ├── globals.css
│   ├── tailwind.css
│   └── themes.css
├── utils/
│   ├── calculatePercentage.ts
│   ├── formatDate.ts
│   └── permissions.ts
├── App.tsx
├── index.css
└── main.tsx